pipeline {
  agent any
  options {
    skipDefaultCheckout(true)
  }
  stages{
    stage('clean workspace') {
      steps {
        cleanWs()
      }
    }
    stage('checkout') {
      steps {
        checkout scm
      }
    }
    stage('terraform-init') {
      steps {
        withAWS(credentials: 'aadi_aws', region: 'us-east-2') {
          sh '''
           terraform init
         '''z
         }
      }
    }
    stage('tflint') {
      agent {
        docker { 
          image 'wata727/tflint' 
          reuseNode true
        }
      }
      steps {
        sh '''
          tflint . --no-color
        '''
      }
    }
    stage('terraform-apply-and-destroy') {
      steps {
        withAWS(credentials: 'aadi_aws', region: 'us-east-2') {  
          sh '''
	   terraform apply -auto-approve -no-color
	   terraform destroy -auto-approve -no-color
         '''
         }
      }
    }
  }
  post {
    always {
      cleanWs()
    }
  }
}


