# Deploy to Azure Function using Jenkins

---

This tutorial shows you how to deploy a Java function to Azure Function using [Azure Function Plugin](https://github.com/jenkinsci/azure-function-plugin). Below are the major steps in this tutorial.

- [Create Azure Function](#create-function)
- [Prepare Jenkins server](#prepare)
- [Create job](#create-job)
- [Build and Deploy Java Funtion to Azure Function](#deploy)
- [Clean Up Resources](#clean-up)


## <a name="create-function"></a>Create Azure Function

Create an Azure Function with Java runtime stack by [Azure portal](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-azure-function) or [Azure CLI](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-azure-function-azure-cli).


## <a name="prepare"></a>Prepare Jenkins server

1. Deploy a [Jenkins Master](https://aka.ms/jenkins-on-azure) on Azure.

1. Install maven using command 'sudo apt install -y maven'.

1. Install [Azure Functions Core Tools](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local).

1. Install the plugins in Jenkins. Click 'Manage Jenkins' -> 'Manage Plugins' -> 'Available', 
then search and install the following plugins: Azure Function Plugin, EnvInject Plugin.

1. Add a Credential in type "Microsoft Azure Service Principal" with your service principal.


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