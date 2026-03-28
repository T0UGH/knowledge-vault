# install/azure

Source: https://docs.openclaw.ai/install/azure

Title: Azure - OpenClaw

URL Source: https://docs.openclaw.ai/install/azure

Markdown Content:
# Azure - OpenClaw

[Skip to main content](https://docs.openclaw.ai/install/azure#content-area)

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

Hosting

Azure

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

*   [OpenClaw on Azure Linux VM](https://docs.openclaw.ai/install/azure#openclaw-on-azure-linux-vm)
*   [What you will do](https://docs.openclaw.ai/install/azure#what-you-will-do)
*   [What you need](https://docs.openclaw.ai/install/azure#what-you-need)
*   [Configure deployment](https://docs.openclaw.ai/install/azure#configure-deployment)
*   [Deploy Azure resources](https://docs.openclaw.ai/install/azure#deploy-azure-resources)
*   [Install OpenClaw](https://docs.openclaw.ai/install/azure#install-openclaw)
*   [Cost considerations](https://docs.openclaw.ai/install/azure#cost-considerations)
*   [Cleanup](https://docs.openclaw.ai/install/azure#cleanup)
*   [Next steps](https://docs.openclaw.ai/install/azure#next-steps)

Hosting

# Azure

# [​](https://docs.openclaw.ai/install/azure#openclaw-on-azure-linux-vm)

OpenClaw on Azure Linux VM

This guide sets up an Azure Linux VM with the Azure CLI, applies Network Security Group (NSG) hardening, configures Azure Bastion for SSH access, and installs OpenClaw.
## [​](https://docs.openclaw.ai/install/azure#what-you-will-do)

What you will do

*   Create Azure networking (VNet, subnets, NSG) and compute resources with the Azure CLI
*   Apply Network Security Group rules so VM SSH is allowed only from Azure Bastion
*   Use Azure Bastion for SSH access (no public IP on the VM)
*   Install OpenClaw with the installer script
*   Verify the Gateway

## [​](https://docs.openclaw.ai/install/azure#what-you-need)

What you need

*   An Azure subscription with permission to create compute and network resources
*   Azure CLI installed (see [Azure CLI install steps](https://learn.microsoft.com/cli/azure/install-azure-cli) if needed)
*   An SSH key pair (the guide covers generating one if needed)
*   ~20-30 minutes

## [​](https://docs.openclaw.ai/install/azure#configure-deployment)

Configure deployment

1

[](https://docs.openclaw.ai/install/azure#)

Sign in to Azure CLI

```
az login
az extension add -n ssh
```

The `ssh` extension is required for Azure Bastion native SSH tunneling.

2

[](https://docs.openclaw.ai/install/azure#)

Register required resource providers (one-time)

```
az provider register --namespace Microsoft.Compute
az provider register --namespace Microsoft.Network
```

Verify registration. Wait until both show `Registered`.

```
az provider show --namespace Microsoft.Compute --query registrationState -o tsv
az provider show --namespace Microsoft.Network --query registrationState -o tsv
```

3

[](https://docs.openclaw.ai/install/azure#)

Set deployment variables

```
RG="rg-openclaw"
LOCATION="westus2"
VNET_NAME="vnet-openclaw"
VNET_PREFIX="10.40.0.0/16"
VM_SUBNET_NAME="snet-openclaw-vm"
VM_SUBNET_PREFIX="10.40.2.0/24"
BASTION_SUBNET_PREFIX="10.40.1.0/26"
NSG_NAME="nsg-openclaw-vm"
VM_NAME="vm-openclaw"
ADMIN_USERNAME="openclaw"
BASTION_NAME="bas-openclaw"
BASTION_PIP_NAME="pip-openclaw-bastion"
```

Adjust names and CIDR ranges to fit your environment. The Bastion subnet must be at least `/26`.

4

[](https://docs.openclaw.ai/install/azure#)

Select SSH key

Use your existing public key if you have one:

```
SSH_PUB_KEY="$(cat ~/.ssh/id_ed25519.pub)"
```

If you don’t have an SSH key yet, generate one:

```
ssh-keygen -t ed25519 -a 100 -f ~/.ssh/id_ed25519 -C "you@example.com"
SSH_PUB_KEY="$(cat ~/.ssh/id_ed25519.pub)"
```

5

[](https://docs.openclaw.ai/install/azure#)

Select VM size and OS disk size

```
VM_SIZE="Standard_B2as_v2"
OS_DISK_SIZE_GB=64
```

Choose a VM size and OS disk size available in your subscription and region:
*   Start smaller for light usage and scale up later
*   Use more vCPU/RAM/disk for heavier automation, more channels, or larger model/tool workloads
*   If a VM size is unavailable in your region or subscription quota, pick the closest available SKU

List VM sizes available in your target region:

```
az vm list-skus --location "${LOCATION}" --resource-type virtualMachines -o table
```

Check your current vCPU and disk usage/quota:

```
az vm list-usage --location "${LOCATION}" -o table
```

## [​](https://docs.openclaw.ai/install/azure#deploy-azure-resources)

Deploy Azure resources

1

[](https://docs.openclaw.ai/install/azure#)

Create the resource group

```
az group create -n "${RG}" -l "${LOCATION}"
```

2

[](https://docs.openclaw.ai/install/azure#)

Create the network security group

Create the NSG and add rules so only the Bastion subnet can SSH into the VM.

```
az network nsg create \
  -g "${RG}" -n "${NSG_NAME}" -l "${LOCATION}"

# Allow SSH from the Bastion subnet only
az network nsg rule create \
  -g "${RG}" --nsg-name "${NSG_NAME}" \
  -n AllowSshFromBastionSubnet --priority 100 \
  --access Allow --direction Inbound --protocol Tcp \
  --source-address-prefixes "${BASTION_SUBNET_PREFIX}" \
  --destination-port-ranges 22

# Deny SSH from the public internet
az network nsg rule create \
  -g "${RG}" --nsg-name "${NSG_NAME}" \
  -n DenyInternetSsh --priority 110 \
  --access Deny --direction Inbound --protocol Tcp \
  --source-address-prefixes Internet \
  --destination-port-ranges 22

# Deny SSH from other VNet sources
az network nsg rule create \
  -g "${RG}" --nsg-name "${NSG_NAME}" \
  -n DenyVnetSsh --priority 120 \
  --access Deny --direction Inbound --protocol Tcp \
  --source-address-prefixes VirtualNetwork \
  --destination-port-ranges 22
```

The rules are evaluated by priority (lowest number first): Bastion traffic is allowed at 100, then all other SSH is blocked at 110 and 120.

3

[](https://docs.openclaw.ai/install/azure#)

Create the virtual network and subnets

Create the VNet with the VM subnet (NSG attached), then add the Bastion subnet.

```
az network vnet create \
  -g "${RG}" -n "${VNET_NAME}" -l "${LOCATION}" \
  --address-prefixes "${VNET_PREFIX}" \
  --subnet-name "${VM_SUBNET_NAME}" \
  --subnet-prefixes "${VM_SUBNET_PREFIX}"

# Attach the NSG to the VM subnet
az network vnet subnet update \
  -g "${RG}" --vnet-name "${VNET_NAME}" \
  -n "${VM_SUBNET_NAME}" --nsg "${NSG_NAME}"

# AzureBastionSubnet — name is required by Azure
az network vnet subnet create \
  -g "${RG}" --vnet-name "${VNET_NAME}" \
  -n AzureBastionSubnet \
  --address-prefixes "${BASTION_SUBNET_PREFIX}"
```

4

[](https://docs.openclaw.ai/install/azure#)

Create the VM

The VM has no public IP. SSH access is exclusively through Azure Bastion.

```
az vm create \
  -g "${RG}" -n "${VM_NAME}" -l "${LOCATION}" \
  --image "Canonical:ubuntu-24_04-lts:server:latest" \
  --size "${VM_SIZE}" \
  --os-disk-size-gb "${OS_DISK_SIZE_GB}" \
  --storage-sku StandardSSD_LRS \
  --admin-username "${ADMIN_USERNAME}" \
  --ssh-key-values "${SSH_PUB_KEY}" \
  --vnet-name "${VNET_NAME}" \
  --subnet "${VM_SUBNET_NAME}" \
  --public-ip-address "" \
  --nsg ""
```

`--public-ip-address ""` prevents a public IP from being assigned. `--nsg ""` skips creating a per-NIC NSG (the subnet-level NSG handles security).**Reproducibility:** The command above uses `latest` for the Ubuntu image. To pin a specific version, list available versions and replace `latest`:

```
az vm image list \
  --publisher Canonical --offer ubuntu-24_04-lts \
  --sku server --all -o table
```

5

[](https://docs.openclaw.ai/install/azure#)

Create Azure Bastion

Azure Bastion provides managed SSH access to the VM without exposing a public IP. Standard SKU with tunneling is required for CLI-based `az network bastion ssh`.

```
az network public-ip create \
  -g "${RG}" -n "${BASTION_PIP_NAME}" -l "${LOCATION}" \
  --sku Standard --allocation-method Static

az network bastion create \
  -g "${RG}" -n "${BASTION_NAME}" -l "${LOCATION}" \
  --vnet-name "${VNET_NAME}" \
  --public-ip-address "${BASTION_PIP_NAME}" \
  --sku Standard --enable-tunneling true
```

Bastion provisioning typically takes 5-10 minutes but can take up to 15-30 minutes in some regions.

## [​](https://docs.openclaw.ai/install/azure#install-openclaw)

Install OpenClaw

1

[](https://docs.openclaw.ai/install/azure#)

SSH into the VM through Azure Bastion

```
VM_ID="$(az vm show -g "${RG}" -n "${VM_NAME}" --query id -o tsv)"

az network bastion ssh \
  --name "${BASTION_NAME}" \
  --resource-group "${RG}" \
  --target-resource-id "${VM_ID}" \
  --auth-type ssh-key \
  --username "${ADMIN_USERNAME}" \
  --ssh-key ~/.ssh/id_ed25519
```

2

[](https://docs.openclaw.ai/install/azure#)

Install OpenClaw (in the VM shell)

```
curl -fsSL https://openclaw.ai/install.sh -o /tmp/install.sh
bash /tmp/install.sh
rm -f /tmp/install.sh
```

The installer installs Node LTS and dependencies if not already present, installs OpenClaw, and launches the onboarding wizard. See [Install](https://docs.openclaw.ai/install) for details.

3

[](https://docs.openclaw.ai/install/azure#)

Verify the Gateway

After onboarding completes:

```
openclaw gateway status
```

Most enterprise Azure teams already have GitHub Copilot licenses. If that is your case, we recommend choosing the GitHub Copilot provider in the OpenClaw onboarding wizard. See [GitHub Copilot provider](https://docs.openclaw.ai/providers/github-copilot).

## [​](https://docs.openclaw.ai/install/azure#cost-considerations)

Cost considerations

Azure Bastion Standard SKU runs approximately **$140/month** and the VM (Standard_B2as_v2) runs approximately **$55/month**.To reduce costs:
*   **Deallocate the VM** when not in use (stops compute billing; disk charges remain). The OpenClaw Gateway will not be reachable while the VM is deallocated — restart it when you need it live again:  ```
az vm deallocate -g "${RG}" -n "${VM_NAME}"
az vm start -g "${RG}" -n "${VM_NAME}"   # restart later
```    
*   **Delete Bastion when not needed** and recreate it when you need SSH access. Bastion is the largest cost component and takes only a few minutes to provision.
*   **Use the Basic Bastion SKU** (~$38/month) if you only need Portal-based SSH and don’t require CLI tunneling (`az network bastion ssh`).

## [​](https://docs.openclaw.ai/install/azure#cleanup)

Cleanup

To delete all resources created by this guide:

```
az group delete -n "${RG}" --yes --no-wait
```

This removes the resource group and everything inside it (VM, VNet, NSG, Bastion, public IP).
## [​](https://docs.openclaw.ai/install/azure#next-steps)

Next steps

*   Set up messaging channels: [Channels](https://docs.openclaw.ai/channels)
*   Pair local devices as nodes: [Nodes](https://docs.openclaw.ai/nodes)
*   Configure the Gateway: [Gateway configuration](https://docs.openclaw.ai/gateway/configuration)
*   For more details on OpenClaw Azure deployment with the GitHub Copilot model provider: [OpenClaw on Azure with GitHub Copilot](https://github.com/johnsonshi/openclaw-azure-github-copilot)

[Podman](https://docs.openclaw.ai/install/podman)[DigitalOcean](https://docs.openclaw.ai/install/digitalocean)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
