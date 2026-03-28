# install/development-channels

Source: https://docs.openclaw.ai/install/development-channels

Title: Release Channels - OpenClaw

URL Source: https://docs.openclaw.ai/install/development-channels

Markdown Content:
# Release Channels - OpenClaw

[Skip to main content](https://docs.openclaw.ai/install/development-channels#content-area)

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

Maintenance

Release Channels

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

*   [Development channels](https://docs.openclaw.ai/install/development-channels#development-channels)
*   [Switching channels](https://docs.openclaw.ai/install/development-channels#switching-channels)
*   [One-off version or tag targeting](https://docs.openclaw.ai/install/development-channels#one-off-version-or-tag-targeting)
*   [Dry run](https://docs.openclaw.ai/install/development-channels#dry-run)
*   [Plugins and channels](https://docs.openclaw.ai/install/development-channels#plugins-and-channels)
*   [Checking current status](https://docs.openclaw.ai/install/development-channels#checking-current-status)
*   [Tagging best practices](https://docs.openclaw.ai/install/development-channels#tagging-best-practices)
*   [macOS app availability](https://docs.openclaw.ai/install/development-channels#macos-app-availability)

Maintenance

# Release Channels

# [​](https://docs.openclaw.ai/install/development-channels#development-channels)

Development channels

OpenClaw ships three update channels:
*   **stable**: npm dist-tag `latest`. Recommended for most users.
*   **beta**: npm dist-tag `beta` (builds under test).
*   **dev**: moving head of `main` (git). npm dist-tag: `dev` (when published). The `main` branch is for experimentation and active development. It may contain incomplete features or breaking changes. Do not use it for production gateways.

We ship builds to **beta**, test them, then **promote a vetted build to `latest`** without changing the version number — dist-tags are the source of truth for npm installs.
## [​](https://docs.openclaw.ai/install/development-channels#switching-channels)

Switching channels

```
openclaw update --channel stable
openclaw update --channel beta
openclaw update --channel dev
```

`--channel` persists your choice in config (`update.channel`) and aligns the install method:
*   **`stable`/`beta`** (package installs): updates via the matching npm dist-tag.
*   **`stable`/`beta`** (git installs): checks out the latest matching git tag.
*   **`dev`**: ensures a git checkout (default `~/openclaw`, override with `OPENCLAW_GIT_DIR`), switches to `main`, rebases on upstream, builds, and installs the global CLI from that checkout.

Tip: if you want stable + dev in parallel, keep two clones and point your gateway at the stable one.
## [​](https://docs.openclaw.ai/install/development-channels#one-off-version-or-tag-targeting)

One-off version or tag targeting

Use `--tag` to target a specific dist-tag, version, or package spec for a single update **without** changing your persisted channel:

```
# Install a specific version
openclaw update --tag 2026.3.27

# Install from the beta dist-tag (one-off, does not persist)
openclaw update --tag beta

# Install from GitHub main branch (npm tarball)
openclaw update --tag main

# Install a specific npm package spec
openclaw update --tag openclaw@2026.3.27
```

Notes:
*   `--tag` applies to **package (npm) installs only**. Git installs ignore it.
*   The tag is not persisted. Your next `openclaw update` uses your configured channel as usual.
*   Downgrade protection: if the target version is older than your current version, OpenClaw prompts for confirmation (skip with `--yes`).

## [​](https://docs.openclaw.ai/install/development-channels#dry-run)

Dry run

Preview what `openclaw update` would do without making changes:

```
openclaw update --dry-run
openclaw update --channel beta --dry-run
openclaw update --tag 2026.3.27 --dry-run
openclaw update --dry-run --json
```

The dry run shows the effective channel, target version, planned actions, and whether a downgrade confirmation would be required.
## [​](https://docs.openclaw.ai/install/development-channels#plugins-and-channels)

Plugins and channels

When you switch channels with `openclaw update`, OpenClaw also syncs plugin sources:
*   `dev` prefers bundled plugins from the git checkout.
*   `stable` and `beta` restore npm-installed plugin packages.
*   npm-installed plugins are updated after the core update completes.

## [​](https://docs.openclaw.ai/install/development-channels#checking-current-status)

Checking current status

```
openclaw update status
```

Shows the active channel, install kind (git or package), current version, and source (config, git tag, git branch, or default).
## [​](https://docs.openclaw.ai/install/development-channels#tagging-best-practices)

Tagging best practices

*   Tag releases you want git checkouts to land on (`vYYYY.M.D` for stable, `vYYYY.M.D-beta.N` for beta).
*   `vYYYY.M.D.beta.N` is also recognized for compatibility, but prefer `-beta.N`.
*   Legacy `vYYYY.M.D-<patch>` tags are still recognized as stable (non-beta).
*   Keep tags immutable: never move or reuse a tag.
*   npm dist-tags remain the source of truth for npm installs:
    *   `latest` -> stable
    *   `beta` -> candidate build
    *   `dev` -> main snapshot (optional)

## [​](https://docs.openclaw.ai/install/development-channels#macos-app-availability)

macOS app availability

Beta and dev builds may **not** include a macOS app release. That is OK:
*   The git tag and npm dist-tag can still be published.
*   Call out “no macOS build for this beta” in release notes or changelog.

[Uninstall](https://docs.openclaw.ai/install/uninstall)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
