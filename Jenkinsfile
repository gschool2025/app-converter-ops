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
                script {
                    env.VERSION = powershell(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${VERSION}")
                }
            }
        }

        stage('Cleanup Existing Container') {
            steps {
                powershell '''
                    $containers = docker ps -a -q --filter "name=app-converter-container"
                    if ($containers) {
                        docker stop $containers
                        docker rm $containers
                    }
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    docker.image("${DOCKER_IMAGE}:${VERSION}").run("-p ${PORT}:${PORT} --name ${CONTAINER_NAME} -d")
                }
            }
        }

        stage('Tag and Push Latest') {
            steps {
                powershell """
                    docker tag ${DOCKER_IMAGE}:${VERSION} ${DOCKER_IMAGE}:latest
                    # Optional: uncomment to push
                    # docker push ${DOCKER_IMAGE}:${VERSION}
                    # docker push ${DOCKER_IMAGE}:latest
                """
            }
        }
    }

    post {
        failure {
            echo "Build failed. Running rollback container..."

            powershell '''
                docker stop app-converter-container || Write-Host "No container to stop"
                docker rm app-converter-container || Write-Host "No container to remove"
                docker run -d -p 3000:3000 --name app-converter-container app-converter:latest
                exit $LASTEXITCODE
            '''
        }
    }
}
