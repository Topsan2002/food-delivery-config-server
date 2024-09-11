node {
    def WORKSPACE = "/var/lib/jenkins/workspace/atm-kotlin-basic"
    def dockerImageTag = "atm-kotlin-basic${env.BUILD_NUMBER}"

    try{
        notifyBuild('STARTED')
        stage('Clone Repo'){
            git url: 'https://github.com/Topsan2002/config-server-food-delivery.git',
            branch:'master'
        }
        stage('Build Docker'){
            dockerImage = docker.build("food-delivery-config-server:${env.BUILD_NUMBER}")
        }
        stage('Push to ECR'){
            sh "aws ecr get-login-password --region ap-southeast-2 | docker login --username AWS --password-stdin 211125430268.dkr.ecr.ap-southeast-2.amazonaws.com"
            sh "docker tag food-delivery-config-server:${env.BUILD_NUMBER} 211125430268.dkr.ecr.ap-southeast-2.amazonaws.com/food-delivery-config-server:${env.BUILD_NUMBER}"
            sh "docker push 211125430268.dkr.ecr.ap-southeast-2.amazonaws.com/food-delivery-config-server:latest"
        }
      stage('Deploy docker'){
         echo "Docker Image Tag Name: ${dockerImageTag}"
         sh "docker stop atm-kotlin-basic || true && docker rm atm-kotlin-basic || true"
         sh "docker run --name atm-kotlin-basic -d -p 8081:8081 atm-kotlin-basic:${env.BUILD_NUMBER}"
      }


    }catch(e){
        currentBuild.result = "FAILED"
        throw e
    }finally{
         notifyBuild(currentBuild.result)
    }
}


def notifyBuild(String buildStatus = 'STARTED'){

  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def now = new Date()

  // message
  def subject = "${buildStatus}, Job: ${env.JOB_NAME} FRONTEND - Deployment Sequence: [${env.BUILD_NUMBER}] "
  def summary = "${subject} - Check On: (${env.BUILD_URL}) - Time: ${now}"
  def subject_email = "Spring boot Deployment"
  def details = """<p>${buildStatus} JOB </p>
    <p>Job: ${env.JOB_NAME} - Deployment Sequence: [${env.BUILD_NUMBER}] - Time: ${now}</p>
    <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME}</a>"</p>"""

  // Email notification
  emailext (
     to: "topsan2002@gmail.com",
     subject: subject_email,
     body: details,
     recipientProviders: [[$class: 'DevelopersRecipientProvider']]
  )

}