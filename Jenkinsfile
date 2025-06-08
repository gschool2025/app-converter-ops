pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'app-converter'
        CONTAINER_NAME = 'app-converter-container'
        PORT = '3000'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/gschool2025/app-converter.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(env.DOCKER_IMAGE)
                }
            }
        }

        stage('Cleanup Existing Container') {
            steps {
                script {
                    // Stop and remove any existing container using port 5000
                    powershell '''
                        $containers = docker ps -q --filter "publish=3000"
                        if ($containers) {
                            docker stop $containers
                            docker rm $containers
                        }
                    '''
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    docker.image("${env.DOCKER_IMAGE}:latest").run("-p ${env.PORT}:${env.PORT} --name ${env.CONTAINER_NAME} -d")
                }
            }
        }
    }
}
