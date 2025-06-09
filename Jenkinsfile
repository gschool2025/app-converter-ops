pipeline {
    agent any

    environment {
        APP_REPO = 'https://github.com/gschool2025/app-converter.git'
        IMAGE_NAME = 'converter-app'
        CONTAINER_NAME = 'app-converter-container'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: "${APP_REPO}"
            }
        }

        stage('Lint Code with ESLint') {
            steps {
                bat 'npm install'
                bat 'npx eslint .'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME% ."
            }
        }

        stage('Cleanup Existing Container') {
            steps {
                // Try to stop and remove container if it exists
                bat 'docker stop %CONTAINER_NAME% || exit 0'
                bat 'docker rm %CONTAINER_NAME% || exit 0'
            }
        }

        stage('Run Docker Container') {
            steps {
                bat "docker run -d -p 3000:3000 --name %CONTAINER_NAME% %IMAGE_NAME%"
            }
        }

        stage('Tag and Push Latest') {
            steps {
                // Optional: only if you want to push to Docker Hub
                // withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                //     bat 'docker login -u %USER% -p %PASS%'
                //     bat 'docker tag %IMAGE_NAME% %USER%/%IMAGE_NAME%:latest'
                //     bat 'docker push %USER%/%IMAGE_NAME%:latest'
                // }
            }
        }
    }

    post {
        failure {
            echo 'Build failed. Attempting rollback...'
            // Stop and remove the container on failure
            bat 'docker stop %CONTAINER_NAME% || exit 0'
            bat 'docker rm %CONTAINER_NAME% || exit 0'
        }
    }
}
