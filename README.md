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

Setup your dev environment by creating a virtual environment

```bash
# virtualenv \path\to\.venv -p path\to\specific_version_python.exe
python3 -m venv .venv
.venv\scripts\activate

deactivate
```

## Style Guidelines

This project enforces quite strict [PEP8](https://www.python.org/dev/peps/pep-0008/) and [PEP257 (Docstring Conventions)](https://www.python.org/dev/peps/pep-0257/) compliance on all code submitted.

We use [Black](https://github.com/psf/black) for uncompromised code formatting.

Summary of the most relevant points:

- Comments should be full sentences and end with a period.
- [Imports](https://www.python.org/dev/peps/pep-0008/#imports) should be ordered.
- Constants and the content of lists and dictionaries should be in alphabetical order.
- It is advisable to adjust IDE or editor settings to match those requirements.


### Use new style string formatting

Prefer [f-strings](https://docs.python.org/3/reference/lexical_analysis.html#f-strings) over ``%`` or ``str.format``.

```python
#New
f"{some_value} {some_other_value}"
# Old, wrong
"{} {}".format("New", "style")
"%s %s" % ("Old", "style")
```

One exception is for logging which uses the percentage formatting. This is to avoid formatting the log message when it is suppressed.

```python
_LOGGER.info("Can't connect to the webservice %s at %s", string1, string2)
```

### Testing
You'll need to install the test dependencies into your Python environment:

```bash
pip3 install -r requirements_dev.txt
```

Now that you have all test dependencies installed, you can run linting and tests on the project:

```bash
isort .
codespell  --skip=".venv"
black generator
flake8 generator
pylint generator
pydocstyle generator
```

# References
