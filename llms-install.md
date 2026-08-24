# Installing the Imaginode MCP server

Imaginode is a **hosted remote MCP server** (Streamable HTTP). There is nothing to build, clone or run locally: you only need the endpoint URL and a user API key.

## 1. Get an API key

1. Create a free account at https://imaginode.ai (trial credits included; a verified email is required).
2. Open https://imaginode.ai/profile, section **API keys**.
3. Create a key and copy the `imk_…` secret (it is shown only once).

## 2. Configure the client

The server needs exactly two things:

- **URL**: `https://imaginode.ai/api/mcp`
- **Header**: `Authorization: Bearer imk_YOUR_KEY`

### Cline (`cline_mcp_settings.json`)

```json
{
  "mcpServers": {
    "imaginode": {
      "type": "streamableHttp",
      "url": "https://imaginode.ai/api/mcp",
      "headers": {
        "Authorization": "Bearer imk_YOUR_KEY"
      }
    }
  }
}
```

### Claude Code

```bash
claude mcp add --transport http imaginode https://imaginode.ai/api/mcp --header "Authorization: Bearer imk_YOUR_KEY"
```

### Cursor (`.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "imaginode": {
      "url": "https://imaginode.ai/api/mcp",
      "headers": { "Authorization": "Bearer imk_YOUR_KEY" }
    }
  }
}
```

## 3. Verify

Ask the assistant to call `list_models`. If the key is valid, it returns the model catalogue with prices in credits. Then try: "Generate an image of a red fox in a misty forest".

## Troubleshooting

- **401 / "Invalid or revoked API key"**: create a new key at https://imaginode.ai/profile and update the header.
- **"Not enough credits"**: top up at https://imaginode.ai/pricing. Failed generations are always refunded automatically.
- **Video seems slow**: video jobs take 1 to 5 minutes (up to 30 for long jobs). Poll `get_generation_status` with the returned `request_id`; never re-launch the same job.
