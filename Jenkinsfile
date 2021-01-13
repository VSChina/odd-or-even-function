node {
    stage('Init') {
        checkout scm
    }

    stage('Build') {
        sh 'mvn clean package'
    }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '/var/lib/jenkins/workspace/myfunctionapp1/target/*.jar', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
    }
}
