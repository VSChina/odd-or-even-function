Deploy to Azure Function using Jenkins
This tutorial shows you how to deploy a Java function to Azure Function using Azure Function Plugin. Below are the major steps in this tutorial.
Create Azure Function
Prepare Jenkins server
Create job
Build and Deploy Java Funtion to Azure Function
Clean Up Resources

Create Azure Function
The Azure Function plugin does not provide the function app if it doesn't already exist. To create an Azure Function with Java runtime stack by using Azure portal or Azure CLI.
Here's briefly how you can create a function app using Azure CLI:
create a resource group by doing: az group create --name myResourceGroup --location eastus
create an Azure storage account: az storage account create --name <storage_name> --location eastus --resource-group myResourceGroup --sku Standard_LRS
create a function app by doing: az functionapp create --resource-group myResourceGroup --consumption-plan-location eastus --name <app_name> --storage-account <storage_name>
Update to version 2.x runtime: az functionapp config appsettings set --name <function_app> --resource-group <my_resource_group> --settings FUNCTIONS_EXTENSION_VERSION=~2

Prepare Jenkins server
Deploy a Jenkins Master on Azure. If you don't have one, view this quickstart to set up one in Azure.
Sign in to the Jenkins instance with SSH and run the following commands:
Install maven using command:  sudo apt install -y maven.
Install Azure Functions Core Tools. E.g., for Ubuntu 16.04 / Linux Mint 18, run the following commands to install :
wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
sudo apt-get install azure-functions-core-tools
In Jenkins dashboard, install the plugins. Click 'Manage Jenkins' -> 'Manage Plugins' -> 'Available', then search and install the following plugins if not already installed: Azure Function Plugin, EnvInject Plugin.
Jenkins needs an Azure service principal for authenticating and accessing Azure resources. Refer to the Crease service principal section in the Deploy to Azure App Service tutorial.
Then using the Azure service principal, add a "Microsoft Azure Service Principal" credential type in Jenkins. Refer to the Add Service principal section in the Deploy to Azure App Service tutorial. This is the [your credential id of service principal] mentioned in Step 2 under "Create Job"

Create job
Add a new job in type "Pipeline".
Enable "Prepare an environment for the run", and add the following environment variables in "Properties Content":
AZURE_CRED_ID=[your credential id of service principal]
RES_GROUP=[your resource group of the function app]
FUNCTION_NAME=[the name of the function]
For [the name of the function], make sure you use the same name when you used to create the function app in Azure.
Choose "Pipeline script from SCM" in "Pipeline" -> "Definition".
Fill in the SCM repo URL and script path. (Script Example)

Build and Deploy Java Function to Azure Function
Run jenkins job.
Open your favorite browser and input https://<function_name>.azurewebsites.net/api/HttpTrigger-Java?code=<key>&number=<input_number> to trigger the function. You will get the output like The number 365 is Odd..
Please refer to Azure Function HTTP triggers and bindings to get the authorization key.

Clean Up Resources
Delete the Azure resources you just created by running below command:
az group delete -y --no-wait -n <your-resource-group-name>
