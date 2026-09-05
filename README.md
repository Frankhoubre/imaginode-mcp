# Imaginode MCP Server

[![LightNow MCP capabilities](https://lightnow.ai/badge/ai.imaginode/imaginode)](https://lightnow.ai/servers/ai.imaginode/imaginode)
[![Imaginode MCP server](https://glama.ai/mcp/servers/Frankhoubre/imaginode-mcp/badges/score.svg)](https://glama.ai/mcp/servers/Frankhoubre/imaginode-mcp)

Generate AI **images and videos** from Claude, Cursor, or any MCP client, powered by [imaginode.ai](https://imaginode.ai), the node-canvas AI creation studio.

- **48+ models on one account**: Flux 2, Nano Banana 2, Seedream 5, GPT Image, Kling V3, Seedance 2.5, Veo 3.1, Hailuo, Wan and more.
- **Transparent pricing**: every generation returns its exact cost in credits (1 credit = €0.01) before the result. Failed generations are refunded automatically.
- **Remote server, nothing to install**: `https://imaginode.ai/api/mcp` (Streamable HTTP).

## Quickstart

1. Create a free account at [imaginode.ai](https://imaginode.ai) (trial credits included, verified email required).
2. Create an API key in [your profile](https://imaginode.ai/profile), section **API keys**. The `imk_…` secret is shown once.
3. Connect your client:

**Claude Code**

```bash
claude mcp add --transport http imaginode https://imaginode.ai/api/mcp --header "Authorization: Bearer imk_YOUR_KEY"
```

**Claude.ai / Claude Desktop** : add a custom connector with URL `https://imaginode.ai/api/mcp` and the same `Authorization` header.

**Cursor** (`.cursor/mcp.json`):

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

Then just ask your assistant: *"Generate a cinematic image of a red fox in a misty forest"* or *"Make a 5-second video of ocean waves at sunset"*.

## Tools

| Tool | What it does |
|---|---|
| `list_models` | Catalogue of image and video models with prices in credits and billable options. |
| `generate_image` | Generates an image and waits for the result: the URL is returned directly. Default model: Nano Banana 2. |
| `generate_video` | Starts a video generation and returns a `request_id` plus the exact cost. Default model: Seedance 2.0. |
| `get_generation_status` | Follows a generation (`running`, `completed` with output URLs, or `failed` with automatic refund). |

Videos typically take 1 to 5 minutes (up to 30 for long jobs): poll `get_generation_status` every 30 seconds.

## Direct REST API

The MCP server is a thin facade over three public endpoints you can also call from any script:

- `GET https://imaginode.ai/api/models` : model catalogue and prices, no auth.
- `POST https://imaginode.ai/api/generate` : body `{ "model", "prompt", "duration", "resolution", "aspectRatio", "audio", "imageUrls" }`, header `Authorization: Bearer imk_…`. Returns `{ requestId, cost, balance }`.
- `GET https://imaginode.ai/api/generate/status?requestId=…` : returns `running`, `completed` (with `outputUrls`) or `failed` (refunded).

Input images must be media hosted on your Imaginode account (media library or previous generations).

Full documentation: [imaginode.ai/docs/api-mcp](https://imaginode.ai/en/docs/api-mcp).

## Limits and security

- API keys are personal secrets: shown once, revocable instantly from your profile, 5 active keys max.
- Each key is rate-limited to 10 generations per minute.
- Every call spends the credits of your own account, at the same prices as the canvas; you can never spend beyond your balance, and failures are refunded.

## Claude skill

The [`skills/imaginode`](skills/imaginode/SKILL.md) folder contains an optional Claude Code skill that teaches Claude how to use this server well (cheap-model-first iteration, cost awareness, polling cadence). Install it by copying the folder to `~/.claude/skills/imaginode`.

## License

MIT. The server itself runs at imaginode.ai; this repository hosts its public manifest, documentation and skill.
