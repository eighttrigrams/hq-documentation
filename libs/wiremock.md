# Wiremock (with Clojure)

This is a super minimal pure wiremock demonstration in Clojure, without using any other wrapper libraries.

`deps.edn`:

```clojure
org.wiremock/wiremock {:mvn/version "3.12.1"}
```

Then, in some namespace:

```clojure
(:import com.github.tomakehurst.wiremock.WireMockServer
           com.github.tomakehurst.wiremock.core.WireMockConfiguration))

(comment
  (def config (new WireMockConfiguration))
  (.port config 1234)
  (def server (new WireMockServer config))
  (.stop server)
  (.start server)
  (require '[clj-http.client :as clj-http])
  (require '[cheshire.core :as json])
  (clj-http/request {:url    "http://localhost:1234/__admin/mappings" 
                     :method :post 
                     :accept :json
                     :as :json
                     :body   (json/generate-string {:request {:method "GET"
                                                              :url "/abc"}
                                                    :response {:status 200
                                                               :body "Hello, world!"}})})
  (:body (clj-http/request {:url "http://localhost:1234/abc" :method :get}))
;; => "Hello, world!"    
)
```

A configurable version:

```clojure
(:import com.github.tomakehurst.wiremock.WireMockServer
         com.github.tomakehurst.wiremock.client.WireMock
         com.github.tomakehurst.wiremock.core.WireMockConfiguration)

(def ^:private wiremock-port
  (with-open [socket (ServerSocket. 0)] (.getLocalPort socket)))

(def wiremock-path "/wiremock")

(defn with-wiremock [f]
  (let [config (new WireMockConfiguration)
        _ (.port config wiremock-port)
        server (new WireMockServer config)
        _ (.start server)]
    (WireMock/configureFor "localhost" wiremock-port)
    (WireMock/stubFor
       (-> (WireMock/post wiremock-path)
           (.willReturn (-> (WireMock/aResponse)
                            (.withStatus 200)
                            (.withBody "hello")))))
    (f)
    (.stop server)))
```

Test with something like this

```
(comment
  (with-wiremock #(clj-http.client/request {:url (str "http://localhost:" wiremock-port wiremock-path),
                                            :method "get"})))
```

## Troubleshooting

```clojure
(.withHeader "Content-Type" "application/json")
```

will lead to


```
; 2025-08-23 09:58:56,792 INFO  org.eclipse.jetty.server.Server [nREPL-session-62ae98a4-8b1e-48a9-b25c-6b000a68e2e7] jetty-11.0.24; built: 2024-08-26T18:11:22.448Z; git: 5dfc59a691b748796f922208956bd1f2794bcd16; jvm 17.0.16+8 MDC: 
; 2025-08-23 09:58:56,803 INFO  org.eclipse.jetty.server.handler.ContextHandler [nREPL-session-62ae98a4-8b1e-48a9-b25c-6b000a68e2e7] Started o.e.j.s.ServletContextHandler@877cfc0{/__admin,null,AVAILABLE} MDC: 
; 2025-08-23 09:58:56,812 INFO  org.eclipse.jetty.server.handler.ContextHandler.ROOT [nREPL-session-62ae98a4-8b1e-48a9-b25c-6b000a68e2e7] RequestHandlerClass from context returned com.github.tomakehurst.wiremock.http.StubRequestHandler. Normalized mapped under returned 'null' MDC: 
; 2025-08-23 09:58:56,812 INFO  org.eclipse.jetty.server.handler.ContextHandler [nREPL-session-62ae98a4-8b1e-48a9-b25c-6b000a68e2e7] Started o.e.j.s.ServletContextHandler@983c307{/,null,AVAILABLE} MDC: 
; 2025-08-23 09:58:56,826 INFO  org.eclipse.jetty.server.AbstractConnector [nREPL-session-62ae98a4-8b1e-48a9-b25c-6b000a68e2e7] Started NetworkTrafficServerConnector@69c032e6{HTTP/1.1, (http/1.1, h2c)}{0.0.0.0:37675} MDC: 
; 2025-08-23 09:58:56,826 INFO  org.eclipse.jetty.server.Server [nREPL-session-62ae98a4-8b1e-48a9-b25c-6b000a68e2e7] Started Server@4e6e8bac{STARTING}[11.0.24,sto=1000] @28522524ms MDC: 
; 2025-08-23 09:58:56,850 INFO  org.eclipse.jetty.server.handler.ContextHandler.__admin [qtp1666159941-1668] RequestHandlerClass from context returned com.github.tomakehurst.wiremock.http.AdminRequestHandler. Normalized mapped under returned 'null' MDC: 
; 2025-08-23 09:58:56,870 INFO  org.eclipse.jetty.server.Server [nREPL-session-62ae98a4-8b1e-48a9-b25c-6b000a68e2e7] Stopped Server@4e6e8bac{STOPPING}[11.0.24,sto=1000] MDC: 
; 2025-08-23 09:58:56,871 INFO  org.eclipse.jetty.server.Server [nREPL-session-62ae98a4-8b1e-48a9-b25c-6b000a68e2e7] Shutdown Server@4e6e8bac{STOPPING}[11.0.24,sto=1000] MDC: 
; 2025-08-23 09:58:56,879 INFO  org.eclipse.jetty.server.AbstractConnector [nREPL-session-62ae98a4-8b1e-48a9-b25c-6b000a68e2e7] Stopped NetworkTrafficServerConnector@69c032e6{HTTP/1.1, (http/1.1, h2c)}{0.0.0.0:37675} MDC: 
; 2025-08-23 09:58:56,887 INFO  org.eclipse.jetty.server.handler.ContextHandler [nREPL-session-62ae98a4-8b1e-48a9-b25c-6b000a68e2e7] Stopped o.e.j.s.ServletContextHandler@983c307{/,null,STOPPED} MDC: 
; 2025-08-23 09:58:56,890 INFO  org.eclipse.jetty.server.handler.ContextHandler [nREPL-session-62ae98a4-8b1e-48a9-b25c-6b000a68e2e7] Stopped o.e.j.s.ServletContextHandler@877cfc0{/__admin,null,STOPPED} MDC: 
; Execution error (ClassCastException) at java.lang.Class/cast (Class.java:3890).
; Cannot cast java.lang.String to [Ljava.lang.String;
```

You'd need to do this

```clojure
(.withHeader "Content-Type" (into-array String ["application/json"]))
```
