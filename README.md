# Deploy to Azure Function using Jenkins

---

This tutorial shows you how to deploy a Java function to Azure Function using [Azure Function Plugin](https://github.com/jenkinsci/azure-function-plugin). Below are the major steps in this tutorial.

- [Create Azure Function](#create-function)
- [Prepare Jenkins server](#prepare)
- [Create job](#create-job)
- [Build and Deploy Java Funtion to Azure Function](#deploy)
- [Clean Up Resources](#clean-up)


## <a name="create-function"></a>Create Azure Function

The Azure Function plugin does not provision the function app if it doesn't already exist. So create an Azure Function with Java runtime stack by using [Azure portal](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-azure-function) or [Azure CLI](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-azure-function-azure-cli).

Here's briefly how you can create a function app using Azure CLI:
* create a resource group by doing: ```az group create --name myResourceGroup --location eastus```
* create an Azure storage account: ```az storage account create --name <storage_name> --location eastus --resource-group myResourceGroup --sku Standard_LRS```
* create a function app by doing: ```az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope --name <app_name> --storage-account  <storage_name>```
* Update to version 2.x runtime: ```az functionapp config appsettings set --name <function_app> --resource-group <my_resource_group> --settings FUNCTIONS_EXTENSION_VERSION=~2```

## <a name="prepare"></a>Prepare Jenkins server

1. Deploy a [Jenkins Master](https://aka.ms/jenkins-on-azure) on Azure. If you don't have one, view the [quickstart](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template) to set up one in Azure.

1. Sign in to the Jenkins instance with SSH and run the following commands:
    *  Install maven using command: ``` sudo apt install -y maven.```
    * Install [Azure Functions Core Tools](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local). E.g., for Ubuntu 16.04 / Linux Mint 18, run the follow to install :
        * ```wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb```
        * ```sudo dpkg -i packages-microsoft-prod.deb```
        * ```sudo apt-get update```
        * ```sudo apt-get install azure-functions-core-tools```

1. In Jenkins dashboard, install the plugins. Click 'Manage Jenkins' -> 'Manage Plugins' -> 'Available', 
then search and install the following plugins if not already installed: Azure Function Plugin, EnvInject Plugin.

1. Jenkins needs an Azure service principal for autheticating and accessing Azure resources. Refer to the [Crease service principal](https://docs.microsoft.com/en-us/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) section in the Deploy to Azure App Service tutorial.

1. Then using the Azure service principal, add a "Microsoft Azure Service Principal" credential type in Jenkins. Refer to the [Add Service principal](https://docs.microsoft.com/en-us/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) section in the Deploy to Azure App Service tutorial.


## <a name="create-job"></a>Create job

1. Add a new job in type "Pipeline".

1. Enable "Prepare an environment for the run", and put the following environment variables
   in "Properties Content":
    ```
    AZURE_CRED_ID=[your credential id of service principal]
    RES_GROUP=[your resource group of the web app]
    FUNCTION_NAME=[the name of the function]
    ```

1. Choose "Pipeline script from SCM" in "Pipeline" -> "Definition".

1. Fill in the SCM repo url and script path. ([Script Example](doc/resources/jenkins/JenkinsFile))


## <a name="deploy"></a>Build and Deploy Java Function to Azure Function

1. Run jenkins job.

1. Open your favorite browser and input `https://<function_name>.azurewebsites.net/api/HttpTrigger-Java?code=<key>&number=<input_number>` to trigger the function. You will get the output like `The number 365 is Odd.`.


## <a name="clean-up"></a>Clean Up Resources

Delete the Azure resources you just created by running below command:

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```