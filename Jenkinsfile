pipeline{
    agent any
    environment{
        DOCKER_HUB_USERNAME = "aiya1607"
        DOCKER_IMAGE_NAME = "myapp"
        DOCKER_TAG = "v${BUILD_NUMBER}"
        IMAGE_URL = "${DOCKER_HUB_USERNAME}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"
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
                        sh "echo ${PASS} | docker login -u ${USER} --passwd-stdin"
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
    }
    post{
        success{
            echo 'Docker Image is build and pushed'
            echo 'Triggering CD Pipeline'
        }
        failure{
            echo 'Something went wrong'
        }
        cleanup{
            cleanWs()
        }
    }
}