pipeline {
  agent any
  stages {
    stage('CheckOutCode') {
      steps {
        git(url: 'https://github.com/DadouneDa/hello-world-war.git', branch: 'DD-dev', changelog: true, credentialsId: 'github_DD')
      }
    }

  }
}