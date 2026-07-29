# Cinatra

The built-in **Cinatra** conversational assistant, packaged as an auto-installed
`agent`-kind extension — the platform's default chat assistant, the one a new
workspace talks to out of the box, expressed as a portable, validated declaration
rather than a hardcoded runtime constant.

**Identity.** Display name **Cinatra**; preferred tag `cinatra`, a built-in and
immutable alias (all assistant tags are lowercase per the owner ruling 2026-07-22
(groganz)). It launches `local` (runs on the host runtime) and delivers turns
`host-runtime` (in-process). It loads the five-router chat skill bundle led by
`chat-assistant-core` and prefers the `anthropic` model provider.

**Credentials.** This package handles no credentials. Model access resolves
through the required `@cinatra-ai/anthropic-connector` runtime dependency, which
owns the Anthropic connection and its keys — the assistant declaration never
carries a secret.

**Configuration.** The authoritative declaration lives in `cinatra/config.json`
under the `assistant` block, validated at install by the shared
assistant-declaration parser (`formatVersion` and `abiVersion` are exactly `1`;
the block is fail-closed on unknown keys).

## Works with

- Anthropic — model provider, via `@cinatra-ai/anthropic-connector`

## Capabilities

- Present the default Cinatra assistant as an installable, routable extension
- Reserve the immutable built-in tag `cinatra` for this assistant
- Resolve model access through the Anthropic connector — no in-package secrets
- Load the five-router chat skill bundle led by `chat-assistant-core` at runtime
