pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'converter-app'
        CONTAINER_PORT = '3000'
    }
    
    stages {
        stage('Checkout App Source') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/gschool2025/app-converter.git'
                    ]]
                ])
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
                    powershell '''
                        $containers = docker ps -q --filter "publish=${env.CONTAINER_PORT}"
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
                    docker.image(env.DOCKER_IMAGE).run("-p ${env.CONTAINER_PORT}:${env.CONTAINER_PORT} -d")
                }
            }
        }
    }
}
