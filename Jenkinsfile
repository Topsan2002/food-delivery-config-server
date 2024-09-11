pipeline {
     agent any
     environment {
          AWS_REGION = 'ap-southeast-1'  // Replace with your region
          AWS_ACCOUNT_ID = '211125430268'  // Replace with your AWS account ID
          ECR_REPO_NAME = 'food-delivery-config-server'  // Replace with your ECR repository name
          IMAGE_TAG = "${env.BUILD_ID}"  // Or use 'latest' or any other tag
          WORKSPACE = "/var/lib/jenkins/workspace/atm-kotlin-basic"
     }

     stages {
             stage('Clone Repo') {
                 steps {
                     git url: 'https://github.com/Topsan2002/config-server-food-delivery.git',
                                 branch:'master'
                 }
             }

             stage('Build Docker Image') {
                 steps {
                     script {
                         dockerImage = docker.build("${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}:${IMAGE_TAG}")
                     }
                 }
             }

             stage('Login to AWS ECR') {
                 steps {
                     script {
                         // Login to ECR
                         sh '''
                         aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                         '''
                     }
                 }
             }

             stage('Push Docker Image to ECR') {
                 steps {
                     script {
                         // Push the Docker image to ECR
                         dockerImage.push()
                     }
                 }
             }
         }
}