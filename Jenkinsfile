pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'converter-app'
        CONTAINER_NAME = 'converter-app-container'
        APP_REPO_URL = 'https://github.com/gschool2025/app-converter.git'
        PORT = '3000'
    }

    stages {
        stage('Checkout App Source') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: "${env.APP_REPO_URL}"
                    ]]
                ])
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('app-converter') {
                    script {
                        docker.build(env.DOCKER_IMAGE)
                    }
                }
            }
        }

        stage('Cleanup Existing Container') {
            steps {
                script {
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
                dir('app-converter') {
                    script {
                        docker.image(env.DOCKER_IMAGE).run("-p ${env.PORT}:${env.PORT} -d --name ${env.CONTAINER_NAME}")
                    }
                }
            }
        }
    }
}
