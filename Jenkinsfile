pipeline {
  agent any

  environment {
    ARM_CLIENT_ID       = credentials('AZURE_CLIENT_ID')
    ARM_CLIENT_SECRET   = credentials('AZURE_CLIENT_SECRET')
    ARM_TENANT_ID       = credentials('AZURE_TENANT_ID')
    ARM_SUBSCRIPTION_ID = credentials('AZURE_SUBSCRIPTION_ID')
  }

  stages {
    stage('Checkout Code') {
      steps {
        git branch: 'main', 
            url: 'https://github.com/shailesh987/Jenkinsfile'
      }
    }

    stage('Terraform Init') {
      steps {
        bat """
          terraform init ^
            -backend-config="subscription_id=%ARM_SUBSCRIPTION_ID%" ^
            -backend-config="client_id=%ARM_CLIENT_ID%" ^
            -backend-config="client_secret=%ARM_CLIENT_SECRET%" ^
            -backend-config="tenant_id=%ARM_TENANT_ID%"
        """
      }
    }

    stage('Terraform Plan') {
      steps {
        bat """
          terraform plan ^
            -var "subscription_id=%ARM_SUBSCRIPTION_ID%" ^
            -var "client_id=%ARM_CLIENT_ID%" ^
            -var "client_secret=%ARM_CLIENT_SECRET%" ^
            -var "tenant_id=%ARM_TENANT_ID%"
        """
      }
    }

    stage('Manual Approval') {
      steps {
        input message: 'Approve deployment?', ok: 'Deploy'
      }
    }

    stage('Terraform Apply') {
      steps {
        bat """
          terraform apply -auto-approve ^
            -var "subscription_id=%ARM_SUBSCRIPTION_ID%" ^
            -var "client_id=%ARM_CLIENT_ID%" ^
            -var "client_secret=%ARM_CLIENT_SECRET%" ^
            -var "tenant_id=%ARM_TENANT_ID%"
        """
      }
    }
  }

  post {
    success {
      echo '✅ Infrastructure provisioned successfully!'
    }
    failure {
      echo '❌ Build failed. Check logs.'
    }
  }
}