# Healthcare App Innovation Demo

This projects demonstrates how to build a Health Checks initiative, which specifically 
entails building a suite of health check applications for an anticipated 7,500 combined direct and partner network 
employees worldwide to use. This uses several Azure Technologies:

* CosmosDB
* Container Services
* API Management Gateway
* GitHub Actions

![Architecture Overview](docs/architecture_overview.jpg "Architecture Overview")

# Prerequisites

Prerequisites:
- Azure CLI
- Azure Subscription
- GitHub Account

## Install Azure CLI

```bash
# Check if installed
az --version

sudo apt-get update
sudo apt-get install ca-certificates curl apt-transport-https lsb-release gnupg
curl -sL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/microsoft.gpg > /dev/null

AZ_REPO=$(lsb_release -cs) 
echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" |  sudo tee /etc/apt/sources.list.d/azure-cli.list

sudo apt-get update
sudo apt-get install azure-cli

az --version
```

# Quick Start

Get started creating a new subscription.

* Clone this project and Create a local.env
* Configure GitHub

## Clone Project

```bash
# clone project
git clone https://github.com/briglx/healthcare_demo.git

# Navigate to Project
cd healthcare_demo

# Set Secrets in .env
AZURE_TENANT_ID=ReplaceWithYourTenantId
AZURE_SUBSCRIPTION_ID=ReplaceWithYourSubscriptionId
GITHUB_ORG=ReplaceWithYourGitHubOrgOrUserName
GITHUB_REPO=RepalceWithYourRepoName

cp example.env .env
sed -i "s/<tenant_id>/$AZURE_TENANT_ID/" .env
sed -i "s/<subscription_id>/$AZURE_SUBSCRIPTION_ID/" .env
sed -i "s/<github_org>/$GITHUB_ORG/" .env
sed -i "s/<github_repo>/$GITHUB_REPO/" .env
```

## Configure GitHub

Create An Azure Active Directory application, with a service principal that has contributor access to your subscription. The application uses OpenId Connect (OIDC) based Federated Identity Credentials.

```bash
# load .env vars (optional)
[ ! -f .env ] || export $(grep -v '^#' .env | xargs)

app_name=github_cicd_service_app
app_secret_name=github_cicd_client_secret

az login --tenant $AZURE_TENANT_ID

# Create an Azure Active Directory application and a service principal.
app_id=$(az ad app create --display-name $app_name --query id -o tsv)
app_client_id=$(az ad app list --display-name $app_name --query [].appId -o tsv)
# Save app_id to .env APP_CLIENT_ID
echo AZURE_CLIENT_ID=$app_client_id >> .env
az ad sp create --id $app_id

# Assign contributor role to the app service principal
app_sp_id=$(az ad sp list --all --display-name $app_name --query "[].id" -o tsv)
az role assignment create --assignee $app_sp_id --role contributor --scope /subscriptions/$AZURE_SUBSCRIPTION_ID
az role assignment create --role contributor --subscription $AZURE_SUBSCRIPTION_ID --assignee-object-id  $app_sp_id --assignee-principal-type ServicePrincipal --scope /subscriptions/$AZURE_SUBSCRIPTION_ID

# Add OIDC federated credentials for the application.
post_body="{\"name\":\"$app_secret_name\","
post_body=$post_body'"issuer":"https://token.actions.githubusercontent.com",'
post_body=$post_body"\"subject\":\"repo:$GITHUB_ORG/$GITHUB_REPO:ref:refs/heads/main\","
post_body=$post_body'"description":"GitHub CICID Service","audiences":["api://AzureADTokenExchange"]}' 
az rest --method POST --uri "https://graph.microsoft.com/beta/applications/$app_id/federatedIdentityCredentials" --body "$post_body"

```

Create GitHub secrets for storing Azure configuration.

- Open your GitHub repository and go to Settings.
- Select Secrets and then New Secret.
- Create secrets for `AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, and `AZURE_SUBSCRIPTION_ID` from values in .env

# Deploy Infrastructure

This setup will deploy the core infrastructure needed to run the the solution:

- Core Infrastructure
- CosmosDB
- Container Service
- ...

## Core infrastructure

Configure the variables.

```bash
# Global
export PROJECT_NAME=healthcare_demo
export RG_NAME="${PROJECT_NAME}"
export RG_REGION=westus
export STORAGE_ACCOUNT_NAME="${PROJECT_NAME}_sa"

# CosmosDB
export COSMOS_NAME="${PROJECT_NAME}-db"

# Container Service
export CS_NAME="${PROJECT_NAME}_sa"

# API Management Gateway
export APIM_NAME="${PROJECT_NAME}_apim"
```

### Resource Group

Create a resource group for this project

```bash
az group create --name $RG_NAME --location $RG_REGION
```

# Development

Setup your dev environment by cloning and opening the project. The project uses DevContainers so be sure to open the project using the container.

```bash
git clone https://github.com/briglx/healthcare_demo.git
cd healthcare_demo
code .
```

# References
- Dotnet Github actions https://learn.microsoft.com/en-us/dotnet/devops/create-dotnet-github-action
- Dotnet Devcontainers https://github.com/microsoft/vscode-remote-try-dotnet