pipeline{
    agent any
    environment{
        DOCKER_HUB_USERNAME = "aiya1607"
        DOCKER_IMAGE_NAME = "myapp"
        DOCKER_TAG = "v${BUILD_NUMBER}"
        IMAGE_URL = "${DOCKER_HUB_USERNAME}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"
        SCANNER_HOME = "/opt/sonar-scanner/sonar-scanner-6.2.1.4610"
        PATH = "${SCANNER_HOME}/bin:$PATH"
        SONARSERVER = 'sonarserver'
        SONARSCANNER = 'sonarscanner'
    }
    
    stages{
        stage('ImageBuild'){
            steps{
                sh "docker build -t ${IMAGE_URL} ."
            }
        }
        stage('DockerAuthPush'){
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: 'aiya', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "echo $PASS | docker login -u ${USER}  --password-stdin"
                        sh "docker push ${IMAGE_URL}"
                    }
                }
            }
        }
        stage('RemoveImage'){
            steps{
                sh "docker image rm ${IMAGE_URL}"
                echo "Image removed"
            }
        }
        stage("SonarQube Analysis") {
            steps {
               withSonarQubeEnv("${SONARSERVER}") {
                   sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=demo_project \
                   -Dsonar.projectName=demo_project \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest'''
              }
        }
    }

    //     post {
    //         success {
    //             echo 'Docker Image Build and Pushed'
    //             build job: 'docker-run', 
    //                 parameters: [
    //                     string(name: 'IMAGE_URL', value: "$IMAGE_URL")
    //                 ]
    //         }
    //         failure {
    //             echo 'Something went wrong'
    //         }
    //         cleanup {
    //             cleanWs()
    //         }
    // }
    }
}

