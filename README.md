# Imaginode MCP Server

[![smithery badge](https://smithery.ai/badge/imaginode-ai/imaginode)](https://smithery.ai/servers/imaginode-ai/imaginode)
[![Imaginode MCP server](https://glama.ai/mcp/servers/Frankhoubre/imaginode-mcp/badges/score.svg)](https://glama.ai/mcp/servers/Frankhoubre/imaginode-mcp)

Generate AI **images and videos** from ChatGPT, Claude, Claude Code, Cursor, or any MCP client, powered by [imaginode.ai](https://imaginode.ai), the node-canvas AI creation studio.

- **89 models on one account**: Flux 2, Nano Banana 2, Seedream 5, GPT Image, Kling V3, Seedance 2.5, Veo 3.1, Hailuo, Wan and more. The full catalogue, with a page per model, is at [imaginode.ai/models](https://imaginode.ai/en/models).
- **Sign in, no key to paste**: the server speaks OAuth 2.1. ChatGPT, Claude and Claude Code open an Imaginode page, you sign in, you allow the app. API keys still work for scripts and clients without OAuth.
- **Templates included**: the curated workflows of the Imaginode canvas (product shoots, renovations, video effects, kids books, thumbnails…) are exposed as recipes, with the link that opens them ready to run.
- **Transparent pricing**: every generation returns its exact cost in credits (1 credit = $0.012, or €0.01 when billed in euros) before the result. Failed generations are refunded automatically. Price a run before you make it with the [cost calculator](https://imaginode.ai/en/calculator), which needs no account.
- **Remote server, nothing to install**: `https://imaginode.ai/api/mcp` (Streamable HTTP).

## What it costs before you call it

The API returns the exact cost of every generation, but you often want the
number before writing the call. Two public pages answer that without an
account:

- [Cost calculator](https://imaginode.ai/en/calculator): pick a model, a
  duration and a resolution, read the price in credits and in dollars.
- [`GET /api/models`](https://imaginode.ai/api/models): the same catalogue and
  prices as machine-readable JSON, no auth, so a client can build its own
  estimate.

## Quickstart

1. Create a free account at [imaginode.ai](https://imaginode.ai) (trial credits included, verified email required).
2. Connect your client. Full walkthrough per assistant: [imaginode.ai/mcp](https://imaginode.ai/en/mcp).

**ChatGPT**: Settings, Apps and connectors, Create: paste `https://imaginode.ai/api/mcp`, choose OAuth, sign in to your Imaginode account.

**Claude.ai / Claude Desktop**: Settings, Connectors, Add custom connector: paste `https://imaginode.ai/api/mcp` and sign in when Claude asks.

**Claude Code** (the sign-in opens in your browser on first use):

```bash
claude mcp add --transport http imaginode https://imaginode.ai/api/mcp
```

**Cursor** (`.cursor/mcp.json`, OAuth):

```json
{
  "mcpServers": {
    "imaginode": { "url": "https://imaginode.ai/api/mcp" }
  }
}
```

**With an API key instead** (scripts, clients without OAuth): create a key in [your profile](https://imaginode.ai/profile), section **API keys**, and send it as a header:

```bash
claude mcp add --transport http imaginode https://imaginode.ai/api/mcp --header "Authorization: Bearer imk_YOUR_KEY"
```

Then just ask your assistant: *"Generate a cinematic image of a red fox in a misty forest"*, *"Make a 5-second video of ocean waves at sunset"*, or *"Which Imaginode template turns my selfie into a collectible figurine?"*.

## Tools

| Tool | What it does |
|---|---|
| `list_models` | Catalogue of image and video models with prices in credits and billable options. |
| `list_templates` | The curated canvas templates: what each produces, its models, the approximate cost of one run. |
| `get_template` | Full recipe of a template (nodes, models, prompts, wiring, example renders) and the link that opens it in the canvas. |
| `generate_image` | Generates an image and waits for the result: the URL is returned directly. Default model: Nano Banana 2. |
| `generate_video` | Starts a video generation and returns a `request_id` plus the exact cost. Default model: Seedance 2.0. |
| `get_generation_status` | Follows a generation (`running`, `completed` with output URLs, or `failed` with automatic refund). |

Videos typically take 1 to 5 minutes (up to 30 for long jobs): poll `get_generation_status` every 30 seconds.

## OAuth details (for client authors)

- Discovery: `https://imaginode.ai/.well-known/oauth-authorization-server` and `https://imaginode.ai/.well-known/oauth-protected-resource`.
- Authorization code with PKCE (S256) and refresh tokens (rotated). Dynamic client registration (RFC 7591) and Client ID Metadata Documents are both supported; redirect URIs may be https, loopback http, or a private app scheme.
- Scope: `generate`. An unauthenticated request gets a `401` with `WWW-Authenticate: Bearer resource_metadata="…"`.
- Connected apps can be revoked by the user from their profile, and by the client through the revocation endpoint (RFC 7009).

## Direct REST API

The MCP server is a thin facade over three public endpoints you can also call from any script (API key or OAuth access token in the `Authorization` header):

- `GET https://imaginode.ai/api/models` : model catalogue and prices, no auth.
- `POST https://imaginode.ai/api/generate` : body `{ "model", "prompt", "duration", "resolution", "aspectRatio", "audio", "imageUrls" }`, header `Authorization: Bearer imk_…`. Returns `{ requestId, cost, balance }`.
- `GET https://imaginode.ai/api/generate/status?requestId=…` : returns `running`, `completed` (with `outputUrls`) or `failed` (refunded).

Input images must be media hosted on your Imaginode account (media library or previous generations).

Full documentation: [imaginode.ai/docs/api-mcp](https://imaginode.ai/en/docs/api-mcp).

## Limits and security

- API keys are personal secrets: shown once, revocable instantly from your profile, 5 active keys max.
- Each key, and each OAuth access token, is rate-limited to 10 generations per minute.
- OAuth access tokens live one hour and are refreshed silently; a connected app can be disconnected at any time from the profile, in Connected apps.
- Every call spends the credits of your own account, at the same prices as the canvas; you can never spend beyond your balance, and failures are refunded.

## Claude skill

The [`skills/imaginode`](skills/imaginode/SKILL.md) folder contains an optional Claude Code skill that teaches Claude how to use this server well (cheap-model-first iteration, cost awareness, polling cadence). Install it by copying the folder to `~/.claude/skills/imaginode`.

## License

MIT. The server itself runs at imaginode.ai; this repository hosts its public manifest, documentation and skill.
