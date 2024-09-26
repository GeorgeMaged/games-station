pipeline {
    agent any
    
    environment {
        APP_NAME = "games-everywhere-flask"
        DOCKER_IMAGE = "${APP_NAME}:${BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    // Stop and remove the existing container (if it exists)
                    sh "docker stop ${APP_NAME} || true"
                    sh "docker rm ${APP_NAME} || true"
                    
                    // Run the new container
                    sh "docker run -d --name ${APP_NAME} -p 8080:8080 ${DOCKER_IMAGE}"
                }
            }
        }
    }
    
    post {
        always {
            // Clean up old images to save space
            sh "docker image prune -f"
        }
    }
}