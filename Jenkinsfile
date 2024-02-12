@Library('utils@master') _

def app
def status = 0
github_credentials = usernamePassword(credentialsId: '8d84cb87-b3cc-494d-8791-ff15230c51d5', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_API_TOKEN')

if (env.CHANGE_ID) { // if not PR
  node('main-agent') {
    ansiColor('xterm') {
      stage('Clone repository') {
        deleteDir() // Delete workspace directory for cleanup
        checkout scm // Git plugin, checks out current commit of branch

        try {
          BRANCH_NAME = CHANGE_BRANCH
        } catch (MissingProperyException) {
        }
      }
        
      stage('SonarQube Analysis') {
        withSonarQubeEnv('sonarqube-prod-dockercompose') {
          withCredentials([string(credentialsId: 'sonarqube-prod-jenkins-token', variable: 'SONARQUBE_PROD_TOKEN')]) {
            sh "docker run -u \$(id -u):\$(id -g) --rm -e SONAR_HOST_URL='$SONAR_HOST_URL' -e SONAR_SCANNER_OPTS='-Dsonar.projectKey=Instabug_tracker_api_AYtHco5M7vrPPoHUPcJW -Dsonar.pullrequest.key=" + pullRequest.number + " -Dsonar.pullrequest.branch=" + pullRequest.headRef + " -Dsonar.pullrequest.base=" + pullRequest.base + "' -e SONAR_LOGIN=$SONARQUBE_PROD_TOKEN -v `pwd`:/usr/src sonarsource/sonar-scanner-cli"
          }
        }
      }
    }
  }
}
