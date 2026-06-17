# Deploy to Your Own Azure Subscription

This folder contains the infrastructure-as-code to deploy all Azure resources needed for The IQ Series cookbooks.

## One-Click Deploy

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/iq-series/deploytoazure)

Click the button above to deploy directly from the Azure Portal. You'll be prompted for:
- **User Object ID** — run `az ad signed-in-user show --query id -o tsv` to get yours
- **Resource Prefix** — a short name prefix for all resources (default: `iqs`)
- **Location** — Azure region (must support [agentic retrieval](https://learn.microsoft.com/azure/search/search-region-support))

> **ℹ️ Sample data seeding**
>
> The one-click portal deployment provisions all core resources (AI Search, OpenAI, Foundry project, Blob Storage, RBAC). It does **not** seed the sample data and knowledge base, because that requires running code.
>
> Seeding uses **Azure AD / managed-identity authentication** (`DefaultAzureCredential`) and never uses storage account keys, so it works in tenants that enforce policies blocking key-based access on storage accounts. Seed the data using either:
>
> 1. **Run `deploy.sh` / `deploy.ps1`** (see [Quick Start](#-quick-start) below) — these scripts deploy the infrastructure and then run `infra/scripts/seed_data.py` as a post-deployment step with your Azure AD identity.
> 2. **Run the Episode 1 cookbook**: Open the [Episode 1 cookbook](../1-Foundry-IQ-Unlocking-Knowledge-for-Agents/cookbook/) and run it end-to-end — it indexes the same NASA "Earth at Night" data and creates the knowledge source and knowledge base.

After deployment, copy the output values from the portal and create a `.env` file **inside each episode's `cookbook/` folder** (e.g., `1-Foundry-IQ-Unlocking-Knowledge-for-Agents/cookbook/.env`):

```
SEARCH_ENDPOINT=<searchEndpoint output>
AOAI_ENDPOINT=<openAiEndpoint output>
AOAI_EMBEDDING_MODEL=text-embedding-3-large
AOAI_EMBEDDING_DEPLOYMENT=text-embedding-3-large
AOAI_GPT_MODEL=gpt-4o-mini
AOAI_GPT_DEPLOYMENT=gpt-4o-mini
FOUNDRY_PROJECT_ENDPOINT=<foundryProjectEndpoint output>
FOUNDRY_MODEL_DEPLOYMENT_NAME=gpt-4o-mini
AZURE_AI_SEARCH_CONNECTION_NAME=<searchConnectionName output>
```

## What Gets Deployed

| Resource | Purpose |
|----------|---------|
| **Azure AI Search** (Standard) | Vector search, semantic ranking, agentic retrieval |
| **Azure OpenAI** | `text-embedding-3-large` + `gpt-4o-mini` model deployments |
| **Azure AI Services** | Foundry resource with project management enabled |
| **Foundry Project** | Project for running the IQ Series cookbooks |
| **AI Search Connection** | Connects the Foundry project to your AI Search service |
| **RBAC Role Assignments** | Proper permissions for your user + service-to-service access |

## 📋 Prerequisites

- **Azure Subscription** with permissions to create resources and assign roles
- **Azure CLI** installed and configured ([Install guide](https://learn.microsoft.com/cli/azure/install-azure-cli))
- **Python 3.10+** installed
- A region that supports [agentic retrieval](https://learn.microsoft.com/azure/search/search-region-support) (default: `eastus2`)

## 🚀 Quick Start

### 1. Login to Azure

```bash
az login
```

### 2. Deploy Infrastructure

⏱️ Estimated time: 5-10 minutes

**macOS / Linux:**

```bash
cd infra
./deploy.sh -g "iq-series-rg" -l "eastus2"
```

**Windows (PowerShell):**

```powershell
cd infra
.\deploy.ps1 -ResourceGroupName "iq-series-rg" -Location "eastus2"
```

This will:
- Create the resource group
- Deploy all Azure resources via Bicep
- Set up RBAC role assignments
- Seed the sample data and knowledge base using your Azure AD identity (skip with `-n` / `-SkipSeed`)
- Generate a `.env` file in the repo root with all endpoints

### 3. Run the Cookbooks

```bash
cd 1-Foundry-IQ-Unlocking-Knowledge-for-Agents/cookbook/
# Open foundry-iq-cookbook.ipynb in VS Code or Jupyter
```

The notebooks will automatically read from the `.env` file — no manual configuration needed.

## 🧹 Cleanup

Delete all resources to avoid ongoing charges:

```bash
az group delete --name iq-series-rg --yes --no-wait
```

## 📁 Files

| File | Description |
|------|-------------|
| `main.bicep` | Bicep template defining all Azure resources |
| `azuredeploy.json` | ARM template generated from `main.bicep` (used by the Deploy to Azure button) |
| `main.parameters.json` | Default parameter values |
| `deploy.sh` | Deployment + data seeding script for macOS/Linux |
| `deploy.ps1` | Deployment + data seeding script for Windows PowerShell |
| `scripts/seed_data.py` | Seeds sample data, knowledge source, and knowledge base (Azure AD auth) |
