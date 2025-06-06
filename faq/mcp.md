# MCP Server Setup

To enable the MCP server functionality, set the `mcp.enabled` flag to `true`.

Once enabled, you can connect to the Kafbat UI instance using an MCP client at the following SSE endpoint:
`http://hostname/mcp/sse`.

### Visual Studio Code

To configure MCP in VS Code:

1. Add the MCP server to your settings:

```json
"mcp": {
  "servers": {
    "Kafka UI": {
      "type": "sse",
      "url": "http://hostname:8080/mcp/sse"
    }
  }
}
```

2. Open the co-pilot chat window and switch to agent mode.

### Claude

Claude supports remote MCP servers **only** with an Enterprise subscription. For basic usage, configure a `mcp-remote` proxy:

```json
{
  "mcpServers": {
    "kafka": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "http://localhost:8080/mcp/sse",
        "--transport sse-only",
        "--allow-http"
      ]
    }
  }
}
```
