# Healthcare App Innovation Demo

This projects demonstrates how to build a Health Checks initiative, which specifically 
entails building a suite of health check applications for an anticipated 7,500 combined direct and partner network 
employees worldwide to use. This uses several Azure Technologies:

* CosmosDB
* Container Services
* API Management Gateway
* GitHub Actions

![Architecture Overview](docs/architecture_overview.jpg "Architecture Overview")

# Setup

This setup will deploy the core infrastructure needed to run the the solution:

- Core Infratructure
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