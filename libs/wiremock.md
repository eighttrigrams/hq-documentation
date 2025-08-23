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

(defn with-wiremock [f]
  (let [config (new WireMockConfiguration)
        _ (.port config wiremock-port)
        server (new WireMockServer config)
        _ (.start server)]
    (WireMock/configureFor "localhost" wiremock-port)
    (WireMock/stubFor
       (-> (WireMock/post "/wiremock")
           (.willReturn (-> (WireMock/aResponse)
                            (.withStatus 200)
                            (.withBody "hello")))))
    (f)
    (.stop server)))

(comment
  (with-wiremock 
    (fn []
      (prn "body" (:body (clj-http/request {:method :post :url qlink-uri}))))))
```
