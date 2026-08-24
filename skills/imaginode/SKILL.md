---
name: imaginode
description: Generate AI images and videos through the Imaginode MCP server (imaginode.ai). Use when the user asks to create, generate or illustrate an image, picture, visual, thumbnail, or a short video clip, and the Imaginode MCP tools (generate_image, generate_video, list_models, get_generation_status) are available.
---

# Imaginode: AI image and video generation

You have access to the Imaginode MCP server (tools `list_models`, `generate_image`, `generate_video`, `get_generation_status`). Every call spends credits from the user's imaginode.ai account (1 credit = €0.01), failed generations are refunded automatically, and the exact cost comes back with each call.

## Workflow

1. **Iterate cheap, finish premium.** For exploration or drafts, use `flux-schnell` (1 credit per image). Once the user validates the composition, regenerate with a premium model (`nano-banana-2`, `flux-2`, `seedream-5-pro`). Never burn premium credits on a first attempt unless the user asks for top quality immediately.
2. **Prompts in English, concrete and visual.** Subject, setting, light, style, framing. The user's language does not matter; translate the intent into a strong English prompt and show the user what you sent.
3. **Announce costs.** Each result includes the cost in credits and the remaining balance: relay them briefly ("2 credits, 4,310 left"). If a call fails with "Not enough credits", point the user to https://imaginode.ai/pricing and stop generating.
4. **Video is asynchronous.** `generate_video` returns a `request_id` and the cost immediately. Poll `get_generation_status` about every 30 seconds; typical jobs take 1 to 5 minutes, long ones up to 30. Keep working on other things between polls and never re-launch the same video because it "seems slow": that costs credits.
5. **Model choice.** Call `list_models` when the user names a style or need you cannot map to a model. Guidelines: photorealism → `flux-2` or `imagen`; image editing or character consistency with reference images → `nano-banana-2` or `seedream-5-pro`; cheap video → `kling-std`; video with generated audio → `veo31-lite` or `veo3-fast`; cinematic top-tier video → `seedance` or `kling3-std`.
6. **Input images** (image-to-video, editing) must already be hosted on the user's Imaginode account: reuse output URLs from previous generations, or ask the user to upload the file to their media library at imaginode.ai first.

## Guardrails

- One generation at a time unless the user explicitly asks for a batch; respect the 10 generations/minute per-key limit.
- Never invent an output URL: only relay URLs returned by the tools.
- If the API key is missing or revoked, tell the user to create one at https://imaginode.ai/profile (API keys section) and reconnect the MCP server.
