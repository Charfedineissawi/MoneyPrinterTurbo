---
name: api-provider-services
description: Guidance for API provider integrations, credential handling, authentication, secret redaction, and provider configuration in MoneyPrinterTurbo.
applyTo: "app/services/**/*.py,app/controllers/**/*.py,app/config/**/*.py,test/services/**/*.py,config.example.toml"
---

# API Provider Services

- Keep provider credentials separate by provider and service. Do not reuse an LLM key for a video, material, TTS, or upload provider unless the existing runtime contract explicitly does so.
- Preserve the established precedence for integrations: configured `config.toml` value first, then the provider-specific environment variable when that service supports one. Keep preflight checks and runtime readers identical.
- Never log, print, include in exception text, or expose through API responses an API key, token, secret-bearing URL, key length, or key digest. Redact raw and URL-encoded secrets from network errors.
- The application authentication key (`app.api_key`) is separate from provider credentials. Protected API routes use the `x-api-key` header and must reject missing, wrong, malformed, or duplicate values without revealing the configured key.
- When adding or changing a provider, update the smallest relevant service adapter, configuration example, provider registry/UI metadata, and focused tests. Avoid broad configuration refactors.
- Keep billable provider submissions non-retrying when a response is ambiguous; preserve task IDs and safe, bounded error text for recovery.

## Material-source credential policy

- Pexels remains supported and configurable, but its key is currently suspended and must not be treated as the sole required default credential.
- Pixabay is an acceptable fallback for the default material workflow. If a valid Pixabay key is configured and no usable Pexels key is available, credential validation must consider the material requirement satisfied and the workflow must use Pixabay or select it explicitly.
- If both Pexels and Pixabay keys are configured, preserve explicit `--video-source` choices. Do not silently replace an explicitly selected source.
- If neither source is configured, report both supported options and request one key; do not request Pexels alone.
- Add tests for Pexels-only, Pixabay-only, both keys, neither key, and explicit source selection. Keep key values out of test output.

Reference the canonical behavior in [config.example.toml](../../config.example.toml), [app/controllers/base.py](../../app/controllers/base.py), and provider service modules rather than duplicating their full documentation.
