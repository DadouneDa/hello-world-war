pipeline {
  agent any
  stages {
    stage('CheckOutCode') {
      steps {
        cleanWs()
        git(url: 'https://github.com/DadouneDa/hello-world-war.git', branch: 'DD-dev', changelog: true, credentialsId: 'github_DD')
        slackSend(channel: 'dd_devops', color: '#3EA652', message: "Success: Stage 'Checkout_Code' on job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }

    stage('Compile') {
      steps {
        dir(path: 'hello-world-war') {
          echo "${env.BUILD_ID}"
          sh 'mvn clean package'
        }

        slackSend(channel: 'dd_devops', color: '#3EA652', message: "Success: Stage 'Compile' on job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }

  }
}
