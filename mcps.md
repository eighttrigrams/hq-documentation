# MCPs

## Asana MCP for Claude Code

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


## Notion MCP for Claude Code

Frist create a *Notion* integration (basically the setting up of a token) under https://www.notion.so/profile/integrations

and then, once that is done, put this line in your terminal 

```bash
$ claude mcp add notion-mcp-server --env NOTION_TOKEN=ntn_16<snip>J2YV -- npx @notionhq/notion-mcp-server
```

and run it. Notion should be available to *Claude Code* as an MCP now, when run in the directory the `add` has been done.

## Playwright MCP for Claude Code

Run

```bash
$ npx playwright install
$ claude mcp add playwright -- npx -y @playwright/mcp
```
