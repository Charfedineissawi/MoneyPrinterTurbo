---
name: video-generation-workflow
description: Use when an agent must configure MoneyPrinterTurbo, identify missing credentials, select a material source, run video generation, or deliver the resulting MP4.
---

# MoneyPrinterTurbo Video Generation

Use the existing workflow in [docs/skill/SKILL.md](../../../docs/skill/SKILL.md) and helper [docs/skill/mpt_agent.py](../../../docs/skill/mpt_agent.py). Do not duplicate or replace that workflow unless a focused bug requires it.

## Credential handling

- Ask only for credentials reported as missing by the helper. Never print or request credentials already present in `config.toml` or supported environment variables.
- Never print `config.toml`, API keys, tokens, full credential-bearing command lines, or raw provider error payloads.
- Keep provider credentials isolated. An LLM key does not automatically satisfy a material, video, TTS, or upload provider.
- Preserve explicit paid-generation confirmation requirements before rerunning billable providers.

## Material source fallback

- Pexels remains supported, but its API key is temporarily suspended and must not be mandatory when Pixabay is configured.
- Treat a valid Pixabay key as sufficient for the default material requirement when no usable Pexels key exists; use Pixabay for that run or pass the explicit Pixabay source.
- If both keys exist, keep an explicit source selection authoritative. Do not silently switch away from a user-selected source.
- If neither key exists, ask once for either a Pexels or Pixabay key and explain that Pixabay is currently the recommended available fallback.
- Keep source detection, missing-key reporting, runtime selection, and tests aligned. A preflight pass must not be allowed to fail later because the runtime reads a different key source.

## Verification

Run focused tests for the changed workflow first, then the documented suite from [test/README.md](../../../test/README.md). Prefer mocked provider calls and temporary configs; never use live credentials in tests.
