def notifyBuild(String buildStatus = 'STARTED') {
    // Build status of null means success.
    buildStatus =  buildStatus ?: 'SUCCESS'

    if (buildStatus == 'STARTED') {
        colorCode = '#FFFF00'
    } else if (buildStatus == 'SUCCESS') {
        colorCode = '#00FF00'
    } else {
        colorCode = '#FF0000'
    }

    // Send notification.
    def msg = "${buildStatus}: `${env.JOB_NAME}` #${env.BUILD_NUMBER}:\n${env.BUILD_URL}"
    slackSend(channel: 'dd_devops', color: colorCode, message: msg)
}

pipeline {
    agent any
    try {
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
            withCredentials([usernamePassword(credentialsId: 'Nexus-Docker', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {

                sh ''' docker login -u $USERNAME -p $PASSWORD 127.0.0.1:9001 
                    docker tag module6:${BUILD_ID} 127.0.0.1:9001/module6:${BUILD_ID}
                    docker push 127.0.0.1:9001/module6:${BUILD_ID}
                    docker rmi $(docker images --filter=reference="127.0.0.1:9001/module6*" -q) -f'''
                slackSend(channel: 'dd_devops', color: '#3EA652', message: "Success: Stage 'Checkout_Code' on job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
            }

        }

        } 
    }
    catch (e) {
        currentBuild.result = "FAILED"
        throw e
    } 
    finally {
        stage('Slack notify - End of Build'){
            notifyBuild(currentBuild.result)
        }
    }
}
