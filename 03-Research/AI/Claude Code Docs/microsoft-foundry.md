# microsoft-foundry

Source: https://code.claude.com/docs/en/microsoft-foundry

Title: Claude Code on Microsoft Foundry - Claude Code Docs

URL Source: https://code.claude.com/docs/en/microsoft-foundry

Markdown Content:
# Claude Code on Microsoft Foundry - Claude Code Docs

[Skip to main content](https://code.claude.com/docs/en/microsoft-foundry#content-area)

[Claude Code Docs home page![Image 1: light logo](https://mintcdn.com/claude-code/c5r9_6tjPMzFdDDT/logo/light.svg?fit=max&auto=format&n=c5r9_6tjPMzFdDDT&q=85&s=78fd01ff4f4340295a4f66e2ea54903c)![Image 2: dark logo](https://mintcdn.com/claude-code/c5r9_6tjPMzFdDDT/logo/dark.svg?fit=max&auto=format&n=c5r9_6tjPMzFdDDT&q=85&s=1298a0c3b3a1da603b190d0de0e31712)](https://code.claude.com/docs/en/overview)

![Image 3: US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

⌘K Ask AI

*   [Claude Developer Platform](https://platform.claude.com/)
*   [Claude Code on the Web](https://claude.ai/code)
*   [Claude Code on the Web](https://claude.ai/code)

Search...

Navigation

Deployment

Claude Code on Microsoft Foundry

[Getting started](https://code.claude.com/docs/en/overview)[Build with Claude Code](https://code.claude.com/docs/en/sub-agents)[Deployment](https://code.claude.com/docs/en/third-party-integrations)[Administration](https://code.claude.com/docs/en/setup)[Configuration](https://code.claude.com/docs/en/settings)[Reference](https://code.claude.com/docs/en/cli-reference)[Resources](https://code.claude.com/docs/en/legal-and-compliance)

##### Deployment

*   [Overview](https://code.claude.com/docs/en/third-party-integrations)
*   [Amazon Bedrock](https://code.claude.com/docs/en/amazon-bedrock)
*   [Google Vertex AI](https://code.claude.com/docs/en/google-vertex-ai)
*   [Microsoft Foundry](https://code.claude.com/docs/en/microsoft-foundry)
*   [Network configuration](https://code.claude.com/docs/en/network-config)
*   [LLM gateway](https://code.claude.com/docs/en/llm-gateway)
*   [Development containers](https://code.claude.com/docs/en/devcontainer)

On this page

*   [Prerequisites](https://code.claude.com/docs/en/microsoft-foundry#prerequisites)
*   [Setup](https://code.claude.com/docs/en/microsoft-foundry#setup)
*   [1. Provision Microsoft Foundry resource](https://code.claude.com/docs/en/microsoft-foundry#1-provision-microsoft-foundry-resource)
*   [2. Configure Azure credentials](https://code.claude.com/docs/en/microsoft-foundry#2-configure-azure-credentials)
*   [3. Configure Claude Code](https://code.claude.com/docs/en/microsoft-foundry#3-configure-claude-code)
*   [4. Pin model versions](https://code.claude.com/docs/en/microsoft-foundry#4-pin-model-versions)
*   [Azure RBAC configuration](https://code.claude.com/docs/en/microsoft-foundry#azure-rbac-configuration)
*   [Troubleshooting](https://code.claude.com/docs/en/microsoft-foundry#troubleshooting)
*   [Additional resources](https://code.claude.com/docs/en/microsoft-foundry#additional-resources)

Deployment

# Claude Code on Microsoft Foundry

Copy page

Learn about configuring Claude Code through Microsoft Foundry, including setup, configuration, and troubleshooting.

Copy page

## [​](https://code.claude.com/docs/en/microsoft-foundry#prerequisites)

Prerequisites

Before configuring Claude Code with Microsoft Foundry, ensure you have:
*   An Azure subscription with access to Microsoft Foundry
*   RBAC permissions to create Microsoft Foundry resources and deployments
*   Azure CLI installed and configured (optional - only needed if you don’t have another mechanism for getting credentials)

If you are deploying Claude Code to multiple users, [pin your model versions](https://code.claude.com/docs/en/microsoft-foundry#4-pin-model-versions) to prevent breakage when Anthropic releases new models.

## [​](https://code.claude.com/docs/en/microsoft-foundry#setup)

Setup

### [​](https://code.claude.com/docs/en/microsoft-foundry#1-provision-microsoft-foundry-resource)

1. Provision Microsoft Foundry resource

First, create a Claude resource in Azure:
1.   Navigate to the [Microsoft Foundry portal](https://ai.azure.com/)
2.   Create a new resource, noting your resource name
3.   Create deployments for the Claude models:
    *   Claude Opus
    *   Claude Sonnet
    *   Claude Haiku

### [​](https://code.claude.com/docs/en/microsoft-foundry#2-configure-azure-credentials)

2. Configure Azure credentials

Claude Code supports two authentication methods for Microsoft Foundry. Choose the method that best fits your security requirements.**Option A: API key authentication**
1.   Navigate to your resource in the Microsoft Foundry portal
2.   Go to the **Endpoints and keys** section
3.   Copy **API Key**
4.   Set the environment variable:

```
export ANTHROPIC_FOUNDRY_API_KEY=your-azure-api-key
```

**Option B: Microsoft Entra ID authentication**When `ANTHROPIC_FOUNDRY_API_KEY` is not set, Claude Code automatically uses the Azure SDK [default credential chain](https://learn.microsoft.com/en-us/azure/developer/javascript/sdk/authentication/credential-chains#defaultazurecredential-overview). This supports a variety of methods for authenticating local and remote workloads.On local environments, you commonly may use the Azure CLI:

```
az login
```

When using Microsoft Foundry, the `/login` and `/logout` commands are disabled since authentication is handled through Azure credentials.

### [​](https://code.claude.com/docs/en/microsoft-foundry#3-configure-claude-code)

3. Configure Claude Code

Set the following environment variables to enable Microsoft Foundry:

```
# Enable Microsoft Foundry integration
export CLAUDE_CODE_USE_FOUNDRY=1

# Azure resource name (replace {resource} with your resource name)
export ANTHROPIC_FOUNDRY_RESOURCE={resource}
# Or provide the full base URL:
# export ANTHROPIC_FOUNDRY_BASE_URL=https://{resource}.services.ai.azure.com/anthropic
```

### [​](https://code.claude.com/docs/en/microsoft-foundry#4-pin-model-versions)

4. Pin model versions

Pin specific model versions for every deployment. If you use model aliases (`sonnet`, `opus`, `haiku`) without pinning, Claude Code may attempt to use a newer model version that isn’t available in your Foundry account, breaking existing users when Anthropic releases updates. When you create Azure deployments, select a specific model version rather than “auto-update to latest.”

Set the model variables to match the deployment names you created in step 1:

```
export ANTHROPIC_DEFAULT_OPUS_MODEL='claude-opus-4-6'
export ANTHROPIC_DEFAULT_SONNET_MODEL='claude-sonnet-4-6'
export ANTHROPIC_DEFAULT_HAIKU_MODEL='claude-haiku-4-5'
```

For current and legacy model IDs, see [Models overview](https://platform.claude.com/docs/en/about-claude/models/overview). See [Model configuration](https://code.claude.com/docs/en/model-config#pin-models-for-third-party-deployments) for the full list of environment variables.
## [​](https://code.claude.com/docs/en/microsoft-foundry#azure-rbac-configuration)

Azure RBAC configuration

The `Azure AI User` and `Cognitive Services User` default roles include all required permissions for invoking Claude models.For more restrictive permissions, create a custom role with the following:

```
{
  "permissions": [
    {
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/providers/*"
      ]
    }
  ]
}
```

For details, see [Microsoft Foundry RBAC documentation](https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/rbac-azure-ai-foundry).
## [​](https://code.claude.com/docs/en/microsoft-foundry#troubleshooting)

Troubleshooting

If you receive an error “Failed to get token from azureADTokenProvider: ChainedTokenCredential authentication failed”:
*   Configure Entra ID on the environment, or set `ANTHROPIC_FOUNDRY_API_KEY`.

## [​](https://code.claude.com/docs/en/microsoft-foundry#additional-resources)

Additional resources

*   [Microsoft Foundry documentation](https://learn.microsoft.com/en-us/azure/ai-foundry/what-is-azure-ai-foundry)
*   [Microsoft Foundry models](https://ai.azure.com/explore/models)
*   [Microsoft Foundry pricing](https://azure.microsoft.com/en-us/pricing/details/ai-foundry/)

Was this page helpful?

Yes No

[Google Vertex AI](https://code.claude.com/docs/en/google-vertex-ai)[Network configuration](https://code.claude.com/docs/en/network-config)

⌘I

[Claude Code Docs home page![Image 4: light logo](https://mintcdn.com/claude-code/c5r9_6tjPMzFdDDT/logo/light.svg?fit=max&auto=format&n=c5r9_6tjPMzFdDDT&q=85&s=78fd01ff4f4340295a4f66e2ea54903c)![Image 5: dark logo](https://mintcdn.com/claude-code/c5r9_6tjPMzFdDDT/logo/dark.svg?fit=max&auto=format&n=c5r9_6tjPMzFdDDT&q=85&s=1298a0c3b3a1da603b190d0de0e31712)](https://code.claude.com/docs/en/overview)

[x](https://x.com/AnthropicAI)[linkedin](https://www.linkedin.com/company/anthropicresearch)

Company

[Anthropic](https://www.anthropic.com/company)[Careers](https://www.anthropic.com/careers)[Economic Futures](https://www.anthropic.com/economic-futures)[Research](https://www.anthropic.com/research)[News](https://www.anthropic.com/news)[Trust center](https://trust.anthropic.com/)[Transparency](https://www.anthropic.com/transparency)

Help and security

[Availability](https://www.anthropic.com/supported-countries)[Status](https://status.anthropic.com/)[Support center](https://support.claude.com/)

Learn

[Courses](https://www.anthropic.com/learn)[MCP connectors](https://claude.com/partners/mcp)[Customer stories](https://www.claude.com/customers)[Engineering blog](https://www.anthropic.com/engineering)[Events](https://www.anthropic.com/events)[Powered by Claude](https://claude.com/partners/powered-by-claude)[Service partners](https://claude.com/partners/services)[Startups program](https://claude.com/programs/startups)

Terms and policies

[Privacy choices](https://code.claude.com/docs/en/microsoft-foundry#)[Privacy policy](https://www.anthropic.com/legal/privacy)[Disclosure policy](https://www.anthropic.com/responsible-disclosure-policy)[Usage policy](https://www.anthropic.com/legal/aup)[Commercial terms](https://www.anthropic.com/legal/commercial-terms)[Consumer terms](https://www.anthropic.com/legal/consumer-terms)

Assistant

Responses are generated using AI and may contain mistakes.
