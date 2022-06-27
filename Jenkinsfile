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
         '''
         }
      }
    }
    stage('tflint') {
      agent {
        docker { 
          image 'ghcr.io/terraform-linters/tflint' 
          reuseNode true
        }
      }
      steps {
        sh '''
	  cd fpg-infra
 	  tflint --init
          tflint  --no-color
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


