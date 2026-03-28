# install/ansible

Source: https://docs.openclaw.ai/install/ansible

Title: Ansible - OpenClaw

URL Source: https://docs.openclaw.ai/install/ansible

Markdown Content:
# Ansible - OpenClaw

[Skip to main content](https://docs.openclaw.ai/install/ansible#content-area)

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

Ansible

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

*   [Ansible Installation](https://docs.openclaw.ai/install/ansible#ansible-installation)
*   [Prerequisites](https://docs.openclaw.ai/install/ansible#prerequisites)
*   [What You Get](https://docs.openclaw.ai/install/ansible#what-you-get)
*   [Quick Start](https://docs.openclaw.ai/install/ansible#quick-start)
*   [What Gets Installed](https://docs.openclaw.ai/install/ansible#what-gets-installed)
*   [Post-Install Setup](https://docs.openclaw.ai/install/ansible#post-install-setup)
*   [Quick Commands](https://docs.openclaw.ai/install/ansible#quick-commands)
*   [Security Architecture](https://docs.openclaw.ai/install/ansible#security-architecture)
*   [Manual Installation](https://docs.openclaw.ai/install/ansible#manual-installation)
*   [Updating](https://docs.openclaw.ai/install/ansible#updating)
*   [Troubleshooting](https://docs.openclaw.ai/install/ansible#troubleshooting)
*   [Advanced Configuration](https://docs.openclaw.ai/install/ansible#advanced-configuration)
*   [Related](https://docs.openclaw.ai/install/ansible#related)

Containers

# Ansible

# [​](https://docs.openclaw.ai/install/ansible#ansible-installation)

Ansible Installation

Deploy OpenClaw to production servers with **[openclaw-ansible](https://github.com/openclaw/openclaw-ansible)** — an automated installer with security-first architecture.

The [openclaw-ansible](https://github.com/openclaw/openclaw-ansible) repo is the source of truth for Ansible deployment. This page is a quick overview.

## [​](https://docs.openclaw.ai/install/ansible#prerequisites)

Prerequisites

| Requirement | Details |
| --- | --- |
| **OS** | Debian 11+ or Ubuntu 20.04+ |
| **Access** | Root or sudo privileges |
| **Network** | Internet connection for package installation |
| **Ansible** | 2.14+ (installed automatically by the quick-start script) |

## [​](https://docs.openclaw.ai/install/ansible#what-you-get)

What You Get

*   **Firewall-first security** — UFW + Docker isolation (only SSH + Tailscale accessible)
*   **Tailscale VPN** — secure remote access without exposing services publicly
*   **Docker** — isolated sandbox containers, localhost-only bindings
*   **Defense in depth** — 4-layer security architecture
*   **Systemd integration** — auto-start on boot with hardening
*   **One-command setup** — complete deployment in minutes

## [​](https://docs.openclaw.ai/install/ansible#quick-start)

Quick Start

One-command install:

```
curl -fsSL https://raw.githubusercontent.com/openclaw/openclaw-ansible/main/install.sh | bash
```

## [​](https://docs.openclaw.ai/install/ansible#what-gets-installed)

What Gets Installed

The Ansible playbook installs and configures:
1.   **Tailscale** — mesh VPN for secure remote access
2.   **UFW firewall** — SSH + Tailscale ports only
3.   **Docker CE + Compose V2** — for agent sandboxes
4.   **Node.js 24 + pnpm** — runtime dependencies (Node 22 LTS, currently `22.14+`, remains supported)
5.   **OpenClaw** — host-based, not containerized
6.   **Systemd service** — auto-start with security hardening

The gateway runs directly on the host (not in Docker), but agent sandboxes use Docker for isolation. See [Sandboxing](https://docs.openclaw.ai/gateway/sandboxing) for details.

## [​](https://docs.openclaw.ai/install/ansible#post-install-setup)

Post-Install Setup

1

[](https://docs.openclaw.ai/install/ansible#)

Switch to the openclaw user

```
sudo -i -u openclaw
```

2

[](https://docs.openclaw.ai/install/ansible#)

Run the onboarding wizard

The post-install script guides you through configuring OpenClaw settings.

3

[](https://docs.openclaw.ai/install/ansible#)

Connect messaging providers

Log in to WhatsApp, Telegram, Discord, or Signal:

```
openclaw channels login
```

4

[](https://docs.openclaw.ai/install/ansible#)

Verify the installation

```
sudo systemctl status openclaw
sudo journalctl -u openclaw -f
```

5

[](https://docs.openclaw.ai/install/ansible#)

Connect to Tailscale

Join your VPN mesh for secure remote access.

### [​](https://docs.openclaw.ai/install/ansible#quick-commands)

Quick Commands

```
# Check service status
sudo systemctl status openclaw

# View live logs
sudo journalctl -u openclaw -f

# Restart gateway
sudo systemctl restart openclaw

# Provider login (run as openclaw user)
sudo -i -u openclaw
openclaw channels login
```

## [​](https://docs.openclaw.ai/install/ansible#security-architecture)

Security Architecture

The deployment uses a 4-layer defense model:
1.   **Firewall (UFW)** — only SSH (22) + Tailscale (41641/udp) exposed publicly
2.   **VPN (Tailscale)** — gateway accessible only via VPN mesh
3.   **Docker isolation** — DOCKER-USER iptables chain prevents external port exposure
4.   **Systemd hardening** — NoNewPrivileges, PrivateTmp, unprivileged user

To verify your external attack surface:

```
nmap -p- YOUR_SERVER_IP
```

Only port 22 (SSH) should be open. All other services (gateway, Docker) are locked down.Docker is installed for agent sandboxes (isolated tool execution), not for running the gateway itself. See [Multi-Agent Sandbox and Tools](https://docs.openclaw.ai/tools/multi-agent-sandbox-tools) for sandbox configuration.
## [​](https://docs.openclaw.ai/install/ansible#manual-installation)

Manual Installation

If you prefer manual control over the automation:

1

[](https://docs.openclaw.ai/install/ansible#)

Install prerequisites

```
sudo apt update && sudo apt install -y ansible git
```

2

[](https://docs.openclaw.ai/install/ansible#)

Clone the repository

```
git clone https://github.com/openclaw/openclaw-ansible.git
cd openclaw-ansible
```

3

[](https://docs.openclaw.ai/install/ansible#)

Install Ansible collections

```
ansible-galaxy collection install -r requirements.yml
```

4

[](https://docs.openclaw.ai/install/ansible#)

Run the playbook

```
./run-playbook.sh
```

Alternatively, run directly and then manually execute the setup script afterward:

```
ansible-playbook playbook.yml --ask-become-pass
# Then run: /tmp/openclaw-setup.sh
```

## [​](https://docs.openclaw.ai/install/ansible#updating)

Updating

The Ansible installer sets up OpenClaw for manual updates. See [Updating](https://docs.openclaw.ai/install/updating) for the standard update flow.To re-run the Ansible playbook (for example, for configuration changes):

```
cd openclaw-ansible
./run-playbook.sh
```

This is idempotent and safe to run multiple times.
## [​](https://docs.openclaw.ai/install/ansible#troubleshooting)

Troubleshooting

Firewall blocks my connection

*   Ensure you can access via Tailscale VPN first
*   SSH access (port 22) is always allowed
*   The gateway is only accessible via Tailscale by design

Service will not start

```
# Check logs
sudo journalctl -u openclaw -n 100

# Verify permissions
sudo ls -la /opt/openclaw

# Test manual start
sudo -i -u openclaw
cd ~/openclaw
openclaw gateway run
```

Docker sandbox issues

```
# Verify Docker is running
sudo systemctl status docker

# Check sandbox image
sudo docker images | grep openclaw-sandbox

# Build sandbox image if missing
cd /opt/openclaw/openclaw
sudo -u openclaw ./scripts/sandbox-setup.sh
```

Provider login fails

Make sure you are running as the `openclaw` user:

```
sudo -i -u openclaw
openclaw channels login
```

## [​](https://docs.openclaw.ai/install/ansible#advanced-configuration)

Advanced Configuration

For detailed security architecture and troubleshooting, see the openclaw-ansible repo:
*   [Security Architecture](https://github.com/openclaw/openclaw-ansible/blob/main/docs/security.md)
*   [Technical Details](https://github.com/openclaw/openclaw-ansible/blob/main/docs/architecture.md)
*   [Troubleshooting Guide](https://github.com/openclaw/openclaw-ansible/blob/main/docs/troubleshooting.md)

## [​](https://docs.openclaw.ai/install/ansible#related)

Related

*   [openclaw-ansible](https://github.com/openclaw/openclaw-ansible) — full deployment guide
*   [Docker](https://docs.openclaw.ai/install/docker) — containerized gateway setup
*   [Sandboxing](https://docs.openclaw.ai/gateway/sandboxing) — agent sandbox configuration
*   [Multi-Agent Sandbox and Tools](https://docs.openclaw.ai/tools/multi-agent-sandbox-tools) — per-agent isolation

[Node.js](https://docs.openclaw.ai/install/node)[Bun (Experimental)](https://docs.openclaw.ai/install/bun)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
