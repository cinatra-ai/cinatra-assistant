# cinatra-assistant — AGENTS.md

Extension-specific guidance for `@cinatra-ai/cinatra-assistant`. Read alongside
the shared cinatra extension conventions.

## What this repo is

An `agent`-kind extension that declares the built-in **Cinatra** assistant. An
assistant is an agent-kind extension whose package-root `cinatra/config.json`
carries an `assistant` block (the assistant declaration). This repo ships that
declaration instead of a compilable `cinatra/oas.json` flow — the two are
mutually exclusive (declaration XOR compilable OAS), and an assistant is defined
by its declaration.

## Payload — `cinatra/config.json`

The `assistant` block is the whole payload. It is validated by the single shared
`assistant-declaration` parser (the same one the manifest generator, install
validation, the production store read, and the connector-access-config scan all
use). Invariants the parser enforces, fail-closed:

- `formatVersion` and `abiVersion` are **exactly `1`** — an unknown version
  fails closed rather than being half-understood.
- `.strict()` at every level — any unknown key hard-fails.
- `preferredTag` must be an already-normalized flat token (lowercase `a-z0-9`
  with single `_`/`-` separators). All assistant tags are lowercase per the
  owner ruling 2026-07-22 (groganz); the built-in tag is `cinatra`.
- `launch` and `delivery` are required. This assistant is `launch: local` /
  `delivery: host-runtime`.
- The block must PROJECT to a valid `assistant_config` sidecar (`persona` +
  `skillBundle` are the two load-bearing fields).

## Credentials — none in this package

The assistant handles no credentials. Model access resolves through the required
runtime dependency `@cinatra-ai/anthropic-connector`, which owns the Anthropic
connection and its keys. Never add a key, token, or secret to this repo — the
connector is the credential boundary.

## Cross-kind dependency

`package.json#cinatra.dependencies` carries one edge: a `required` `runtime`
dependency on the `connector`-kind `@cinatra-ai/anthropic-connector` (semver
range pinned to its current published line). The assistant's normal capability
cannot work without a resolved Anthropic connection, so the edge is `required`.

## Identity carry-over (retirement of the built-in assistant)

This package serves the built-in-assistant retirement (cinatra-ai/cinatra#1927).
The host currently seeds an in-code reference (`cinatraAssistantConfig`) and an
immutable `builtin` alias row (`BUILTIN_ASSISTANT_ALIAS`, alias `cinatra` →
`@cinatra-ai/cinatra-assistant`). When this extension becomes the source of
truth, the hand-off of that alias reservation and the reference persona / skill
bundle must be reconciled so the `preferredTag` claim does not collide with the
pre-seeded `builtin` alias. See the PR body for the open carry-over question.

## Validation

Run the self-contained gate locally before pushing:

```
node extension-kind-gate.mjs --package-root .
```

CI runs the same gate (no `@cinatra-ai` dependency, no registry access) plus the
org hygiene gates.
