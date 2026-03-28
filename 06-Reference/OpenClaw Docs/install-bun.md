# install/bun

Source: https://docs.openclaw.ai/install/bun

Title: Bun (Experimental) - OpenClaw

URL Source: https://docs.openclaw.ai/install/bun

Markdown Content:
# Bun (Experimental) - OpenClaw

[Skip to main content](https://docs.openclaw.ai/install/bun#content-area)

[OpenClaw home page![Image 1: dark logo](https://mintcdn.com/clawdhub/dpADRo8IUoiDztzJ/assets/pixel-lobster.svg?fit=max&auto=format&n=dpADRo8IUoiDztzJ&q=85&s=8fdf719fb6d3eaad7c65231385bf28e5)![Image 2: dark logo](https://mintcdn.com/clawdhub/dpADRo8IUoiDztzJ/assets/pixel-lobster.svg?fit=max&auto=format&n=dpADRo8IUoiDztzJ&q=85&s=8fdf719fb6d3eaad7c65231385bf28e5)](https://docs.openclaw.ai/)

![Image 3: US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

⌘K

*   [GitHub](https://github.com/openclaw/openclaw)
*   [Releases](https://github.com/openclaw/openclaw/releases)
*   [Discord](https://discord.com/invite/clawd)

Search...

Navigation

Containers

Bun (Experimental)

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Install overview

*   [Install](https://docs.openclaw.ai/install)
*   [Installer Internals](https://docs.openclaw.ai/install/installer)
*   [Node.js](https://docs.openclaw.ai/install/node)

##### Containers

*   [Ansible](https://docs.openclaw.ai/install/ansible)
*   [Bun (Experimental)](https://docs.openclaw.ai/install/bun)
*   [Docker](https://docs.openclaw.ai/install/docker)
*   [Nix](https://docs.openclaw.ai/install/nix)
*   [Podman](https://docs.openclaw.ai/install/podman)

##### Hosting

*   [Azure](https://docs.openclaw.ai/install/azure)
*   [DigitalOcean](https://docs.openclaw.ai/install/digitalocean)
*   [Docker VM Runtime](https://docs.openclaw.ai/install/docker-vm-runtime)
*   [exe.dev](https://docs.openclaw.ai/install/exe-dev)
*   [Fly.io](https://docs.openclaw.ai/install/fly)
*   [GCP](https://docs.openclaw.ai/install/gcp)
*   [Hetzner](https://docs.openclaw.ai/install/hetzner)
*   [Kubernetes](https://docs.openclaw.ai/install/kubernetes)
*   [Linux Server](https://docs.openclaw.ai/vps)
*   [macOS VMs](https://docs.openclaw.ai/install/macos-vm)
*   [Northflank](https://docs.openclaw.ai/install/northflank)
*   [Oracle Cloud](https://docs.openclaw.ai/install/oracle)
*   [Railway](https://docs.openclaw.ai/install/railway)
*   [Raspberry Pi](https://docs.openclaw.ai/install/raspberry-pi)
*   [Render](https://docs.openclaw.ai/install/render)

##### Maintenance

*   [Updating](https://docs.openclaw.ai/install/updating)
*   [Migration Guide](https://docs.openclaw.ai/install/migrating)
*   [Matrix migration](https://docs.openclaw.ai/install/migrating-matrix)
*   [Uninstall](https://docs.openclaw.ai/install/uninstall)
*   [Release Channels](https://docs.openclaw.ai/install/development-channels)

On this page

*   [Bun (Experimental)](https://docs.openclaw.ai/install/bun#bun-experimental)
*   [Install](https://docs.openclaw.ai/install/bun#install)
*   [Lifecycle Scripts](https://docs.openclaw.ai/install/bun#lifecycle-scripts)
*   [Caveats](https://docs.openclaw.ai/install/bun#caveats)

Containers

# Bun (Experimental)

# [​](https://docs.openclaw.ai/install/bun#bun-experimental)

Bun (Experimental)

Bun is **not recommended for gateway runtime** (known issues with WhatsApp and Telegram). Use Node for production.

Bun is an optional local runtime for running TypeScript directly (`bun run ...`, `bun --watch ...`). The default package manager remains `pnpm`, which is fully supported and used by docs tooling. Bun cannot use `pnpm-lock.yaml` and will ignore it.
## [​](https://docs.openclaw.ai/install/bun#install)

Install

1

[](https://docs.openclaw.ai/install/bun#)

Install dependencies

```
bun install
```

`bun.lock` / `bun.lockb` are gitignored, so there is no repo churn. To skip lockfile writes entirely:

```
bun install --no-save
```

2

[](https://docs.openclaw.ai/install/bun#)

Build and test

```
bun run build
bun run vitest run
```

## [​](https://docs.openclaw.ai/install/bun#lifecycle-scripts)

Lifecycle Scripts

Bun blocks dependency lifecycle scripts unless explicitly trusted. For this repo, the commonly blocked scripts are not required:
*   `@whiskeysockets/baileys``preinstall` — checks Node major >= 20 (OpenClaw defaults to Node 24 and still supports Node 22 LTS, currently `22.14+`)
*   `protobufjs``postinstall` — emits warnings about incompatible version schemes (no build artifacts)

If you hit a runtime issue that requires these scripts, trust them explicitly:

```
bun pm trust @whiskeysockets/baileys protobufjs
```

## [​](https://docs.openclaw.ai/install/bun#caveats)

Caveats

Some scripts still hardcode pnpm (for example `docs:build`, `ui:*`, `protocol:check`). Run those via pnpm for now.

[Ansible](https://docs.openclaw.ai/install/ansible)[Docker](https://docs.openclaw.ai/install/docker)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
