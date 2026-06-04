# omicos-e2e

Post-publish, cross-platform end-to-end test for the
[`@omicverse/omicos`](https://www.npmjs.com/package/@omicverse/omicos) npm
package.

**This repo contains no `omicos-core` source.** It is a black-box consumer
test: once the npm packages are published, it proves that on each of the 6
shipping platforms a user can install omicos and get a working kernel env.

## What it checks (per platform)

| Step | Gate |
|---|---|
| `npm i -g @omicverse/omicos` | the platform package resolves & installs |
| `omicos --help` | the native binary runs on this arch |
| `uv sync` of [`env/`](env) | the bundled kernel env builds (all deps have wheels here) |
| `import omicverse / scanpy / anndata / anndataoom` | the env actually works (no torch needed) |
| `omicos serve` | omicos starts and spawns the shared kernel from that env *(best-effort)* |

Platforms: `linux-x64`, `linux-arm64`, `darwin-x64`, `darwin-arm64`,
`win-x64`, `win-arm64` — each on its own native runner.

## How it runs

- **Manual:** Actions → *omicos-e2e* → Run workflow (optionally pin an
  `omicos_version`).
- **Weekly:** scheduled Monday 06:00 UTC.
- **After a release:** `omicos-core`'s publish workflow can fire it via
  `repository_dispatch`:
  ```bash
  gh api repos/omicverse/omicos-e2e/dispatches -f event_type=npm-published
  ```

## `env/`

A vendored copy of the omicos kernel-env recipe (`pyproject.toml` +
`uv.lock` + `.python-version`) — the same one omicos ships. torch lives in
an opt-in `gpu` extra, so the base `uv sync` installs on every platform.
Keep this in sync with the upstream env recipe when it changes.
