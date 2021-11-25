pipeline {
  agent {
    node {
      label 'docker-vm'
    }

  }
  stages {
    stage('Checkout') {
      steps {
        git(url: 'https://github.com/yonibahar/hello-world-war.git', branch: 'dev', changelog: true)
      }
    }

    stage('Maven Compile') {
      steps {
        sh '''mvn compile
             mvn clean package'''
      }
    }

  }
}
