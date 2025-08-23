# Asana MCP for Claude Code

For Claude Code/Desktop (Free):


Community MCP Server

```
$ claude mcp add asana -e ASANA_ACCESS_TOKEN=<YOUR_TOKEN> -- npx -y @roychri/mcp-server-asana
```

Getting Your Asana Token:

1. Go to https://app.asana.com/0/my-apps
2. Click "Create New Token"
3. Copy the token

Why the Enterprise Requirement Exists:
The official Asana MCP server (https://mcp.asana.com/sse) requires Enterprise because:
- It's hosted by Asana
- Includes enterprise security features
- Has admin controls for workspace owners
But community/local MCP servers work fine with free Claude Desktop/Code because they run locally on your machine.
The Enterprise requirement only applies to the official hosted Asana MCP server, not to local community implementations!
