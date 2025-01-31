pipeline {
  agent any
  environment {
    AZURE_SUBSCRIPTION_ID='c38016af-1cf5-46d4-8828-95ea34bf0348'
    AZURE_TENANT_ID='d79e9c22-774d-4682-8659-0c60a819d63e'
    AZURE_STORAGE_ACCOUNT='jenkins1maven'
    MAVEN_HOME='/usr/local/Cellar/maven/3.8.2/libexec'
    AZURE='/usr/local/Cellar/azure-cli/2.27.2/libexec/bin/python'
    BREW='/usr/local/homebrew/bin'
  }
  // tools { 
  //     maven 'Maven' 
  //   } 

  stages {
    stage('Compile stage') {
      steps {
      sh '''
        PATH=$HOME/bin:/usr/local/bin:$PATH:$MAVEN_HOME:$BREW:$AZURE
        echo $PATH
        cd java-project
        mvn package
        mvn clean compile
        '''
      }
    }

    stage('Testing stage') {
      steps {
      sh '''
        PATH=$HOME/bin:/usr/local/bin:$PATH:$MAVEN_HOME:$BREW:$AZURE
        cd java-project
        mvn test
        '''
      }
    }
    stage('Final Jenkins Pipeline Stage') {
      steps {

      sh "echo 'Jenkins Pipeline Complete'"
      junit "java-project/target/surefire-reports/*.xml"
      }
    }
    stage('Pushing to Azure Storage'){
      steps {
        // azureCLI commands: [[exportVariablesString: '', script: 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'],
        //  [ script: 'az account set --subscription $AZURE_SUBSCRIPTION_ID' ],
        //  [script: 'az storage container create --account-name $AZURE_STORAGE_ACCOUNT --name $JOB_NAME --auth-mode login'],
        //  [script: 'az storage blob upload-batch --destination ${JOB_NAME} --source java-project/target/surefire-reports --account-name $AZURE_STORAGE_ACCOUNT'],
        //  [script: 'az logout']], principalCredentialId: 'azuresp'
        withCredentials([usernamePassword(credentialsId: 'azuresp', 
                        passwordVariable: 'AZURE_CLIENT_SECRET', 
                        usernameVariable: 'AZURE_CLIENT_ID')]) {
          sh '''
            PATH=$HOME/bin:/usr/local/bin:$PATH:$MAVEN_HOME:$BREW:$AZURE
            echo $container_name
            # Login to Azure with ServicePrincipal
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            # Set default subscription
            az account set --subscription $AZURE_SUBSCRIPTION_ID
            # Execute upload to Azure
            az storage container create --account-name $AZURE_STORAGE_ACCOUNT --name $JOB_NAME --auth-mode login
            az storage blob upload-batch --destination ${JOB_NAME} --source java-project/target/surefire-reports --account-name $AZURE_STORAGE_ACCOUNT
            # Logout from Azure
            az logout
          '''
        }
      }
    }
  }
}
