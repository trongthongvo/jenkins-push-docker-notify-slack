pipeline {
  environment {
    imagename = "trongthongvo/2210"
    registryCredential = 'dockerhub-login'
    dockerImage = ''

    SLACK_CHANNEL = "#learn-today-leader-tomorrow"
    SLACK_TEAM_DOMAIN = "systemadminworld"
    SLACK_TOKEN = credentials("slack-notify")
    DEPLOY_URL = "http://zing.vn"

  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git([url: 'https://github.com/trongthongvo/2210.git', branch: 'main'])

      }
    }
   

    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build imagename
        }
        sh '''docker ps -a
              docker images'''
      }
    }


    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push("$BUILD_NUMBER")
             dockerImage.push('latest')

          }
        }
      }
        post {
                success {
                    slackSend (
                        teamDomain: "${env.SLACK_TEAM_DOMAIN}",
                        token: "${env.SLACK_TOKEN}",
                        channel: "${env.SLACK_CHANNEL}",
                        color: "good",
                        message: "${imagename} production deploy with version: *${BUILD_NUMBER}*. <${env.DEPLOY_URL}|Access service> - <${env.BUILD_URL}|Check build>"
                    )
                }

        failure {
                    slackSend (
                        teamDomain: "${env.SLACK_TEAM_DOMAIN}",
                        token: "${env.SLACK_TOKEN}",
                        channel: "${env.SLACK_CHANNEL}",
                        color: "danger",
                      message: "${imagename} production deploy failed: *${env.DEPLOY_VERSION}*. <${env.BUILD_URL}|Check build>"
                    )
                }
            }
      
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $imagename:$BUILD_NUMBER"
        sh "docker rmi $imagename:latest"
        sh "docker images"
        sh "docker ps -a"
      }
    }

}
}


