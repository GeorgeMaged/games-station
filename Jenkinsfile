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
        
        stage('Diagnostics') {
            steps {
                sh 'docker --version'
                sh 'docker info'
                sh 'id'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        sh "docker build -t ${DOCKER_IMAGE} ."
                    } catch (exc) {
                        echo "Docker build failed: ${exc.message}"
                        currentBuild.result = 'FAILURE'
                        error("Stopping early!")
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    try {
                        sh "docker stop ${APP_NAME} || true"
                        sh "docker rm ${APP_NAME} || true"
                        sh "docker run -d --name ${APP_NAME} -p 8080:8080 ${DOCKER_IMAGE}"
                    } catch (exc) {
                        echo "Deployment failed: ${exc.message}"
                        currentBuild.result = 'FAILURE'
                        error("Stopping early!")
                    }
                }
            }
        }
    }
    
    post {
        always {
            sh "docker image prune -f || true"
        }
    }
}