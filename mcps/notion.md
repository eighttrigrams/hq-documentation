# Notion for Claude Code

Frist create a *Notion* integration (basically the setting up of a token) under https://www.notion.so/profile/integrations

and then, once that is done, put this line in your terminal 

```bash
$ claude mcp add notion-mcp-server --env NOTION_TOKEN=ntn_16<snip>J2YV -- npx @notionhq/notion-mcp-server
```

and run it. Notion should be available to *Claude Code* as an MCP now, when run in the directory the `add` has been done.
