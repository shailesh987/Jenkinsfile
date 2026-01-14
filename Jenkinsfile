pipeline {
  agent any

  environment {
    ARM_CLIENT_ID       = credentials('azure-client-id')       // use your real IDs
    ARM_CLIENT_SECRET   = credentials('azure-client-secret')
    ARM_TENANT_ID       = credentials('azure-tenant-id')
    ARM_SUBSCRIPTION_ID = credentials('azure-subscription-id')
  }

  stages {
    stage('Checkout Code') {
      steps {
        git url: 'https://github.com/shailesh987/Jenkinsfile.git'  // or your repo
      }
    }

    stage('Terraform Init') {
      steps {
        bat 'terraform init'   // ← changed from sh
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
    success { echo '✅ Infrastructure provisioned successfully!' }
    failure { echo '❌ Build failed. Check logs.' }
  }
}