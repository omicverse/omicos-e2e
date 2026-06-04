# omicos-e2e

Post-publish, cross-platform end-to-end test for the
[`@omicverse/omicos`](https://www.npmjs.com/package/@omicverse/omicos) npm
package.

**This repo contains no `omicos-core` source and no env recipe.** It is a
pure black-box consumer test: once the npm packages are published, it
proves that on each of the 6 shipping platforms a user can install omicos
and **auto-provision a working kernel env in one click**.

## What it checks (per platform)

| Step | Gate |
|---|---|
| `npm i -g @omicverse/omicos` | the platform package resolves & installs |
| `omicos --help` | the native binary runs on this arch |
| `omicos env setup --yes` | the **first-run bootstrap** auto-installs uv + builds the env baked into the binary (catches any dep without a wheel here) |
| `omicos env doctor` | omicverse is present in the provisioned env |
| `omicos serve` | omicos starts and spawns the kernel from that env *(best-effort)* |

Platforms: `linux-x64`, `linux-arm64`, `darwin-x64`, `darwin-arm64`,
`win-x64`, `win-arm64` — each on its own native runner.

> The kernel env recipe is **baked into the omicos binary** (`omicos env
> setup` writes & syncs it), so this repo no longer vendors it — the test
> exercises exactly the user's one-click path.

## How it runs

- **Automatically after a release:** `omicos-core`'s npm-release workflow
  fires a `repository_dispatch` (`npm-published`) here once the new
  version is on npm.
- **Weekly:** scheduled Monday 06:00 UTC (backstop).
- **Manual:** Actions → *omicos-e2e* → Run workflow (optionally pin an
  `omicos_version`).
