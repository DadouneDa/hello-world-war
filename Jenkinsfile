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
        sh 'mvn clean package'
        slackSend(channel: 'dd_devops', color: '#3EA652', message: "Success: Stage 'Compile' on job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }

    stage('StaticCodeAnalysis') {
      steps {
        sh 'mvn verify sonar:sonar'
        slackSend(channel: 'dd_devops', color: '#3EA652', message: "Success: Stage 'StaticCodeAnalysis' on job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }

    stage('DockerBuild') {
      steps {
        sh '''cp ${WORKSPACE}/target/hello-world-war-1.0.0.war ${WORKSPACE}
                                     docker build -t module6:${BUILD_ID} .'''
        slackSend(channel: 'dd_devops', color: '#3EA652', message: "Success: Stage 'Checkout_Code' on job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")

      }
    }
    stage('Upload_artifact_to_Nexus'){
      
      steps {
        sh ''' docker tag module6:${BUILD_ID} 127.0.0.1:9001/module6:${BUILD_ID}
               docker push 127.0.0.1:9001/module6:${BUILD_ID}
               docker rmi $(docker images --filter=reference="127.0.0.1:9001/module6*" -q) -f'''
        slackSend(channel: 'dd_devops', color: '#3EA652', message: "Success: Stage 'Checkout_Code' on job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }


    }

  }
}
