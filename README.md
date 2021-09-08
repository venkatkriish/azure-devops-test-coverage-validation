# azure-devops-test-coverage-validation
Configure our local environment:
Before you begin, you must have the following:
Azure-Devops Account 
An Azure account with an active subscription. Create an account for free
Azure PowerShell version 5.0 or later.
The azure function core tools version 3.x
Azure CLI version 2.4 or later.
The .NET Core 3.1 SDK

Prerequisite Check:
Verify your prerequisites, which depend on whether you are using Azure CLI or Azure PowerShell for creating Azure resources:
In a terminal or command window, run func --version to check that the Azure Functions Core Tools are version 3.x.
Run az --version to check that the Azure CLI version is 2.4 or later.
Run az login to sign in to Azure and verify an active subscription.

Create a local function project
func init Azure-Test-validation --powershell
cd Azure-Test-validation
func new --name azure=devops-test-validation --template "HTTP trigger" --authlevel "anonymous"

pls find the script for "Azure-Devops-Test-Validation"
run.ps1
https://github.com/venkatkriish/azure-devops-test-coverage-validation/blob/Creating-a-Test-Validation-file-ps/test-validator.ps1

Before you can deploy your function code to Azure, you need to create three resources:
A resource group, which is a logical container for related resources.
A Storage account, which is used to maintain state and other information about your functions.
A function app, which provides the environment for executing your function code. A function app maps to your local function project and lets you group functions as a logical unit for easier management, deployment, and sharing of resources.

Use the following commands to create these items. Both Azure CLI and PowerShell are supported.
If you haven't done so already, sign in to Azure:
az login
Create a resource group named AzureFunctionsQuickstart-rg in your chosen region:

Azure CLI
Azure PowerShell

az group create --name AzureFunctionsQuickstart-rg --location <REGION>

Create a general-purpose storage account in your resource group and region:
Azure CLI
Azure PowerShell

az storage account create --name <STORAGE_NAME> --location <REGION> --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS

The az storage account create command creates the storage account.
Create the function app in Azure:
Azure CLI
Azure PowerShell

az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>

The az functionapp create command creates the function app in Azure.
After you've successfully created your function app in Azure, you're now ready to deploy your local functions project by using the func azure functionapp publish command.
In the following example, replace <APP_NAME> with the name of your app.
func azure functionapp publish <APP_NAME>
The publish command shows results similar to the following output (truncated for simplicity):
...
Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.
...
Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
