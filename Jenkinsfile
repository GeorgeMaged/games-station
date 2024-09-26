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
                sh 'pwd'
                sh 'ls -la'
                sh 'cat Dockerfile || echo "Dockerfile not found"'
                sh 'sudo docker --version || true'
                sh 'sudo docker info || true'
                sh 'id'
                sh 'groups'
                sh 'sudo ls -l /var/run/docker.sock || true'
            }
            }
        
        
        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        sh "sudo docker build -t ${DOCKER_IMAGE} ."
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
                        sh "sudo docker stop ${APP_NAME} || true"
                        sh "sudo docker rm ${APP_NAME} || true"
                        sh "sudo docker run -d --name ${APP_NAME} -p 8080:8080 ${DOCKER_IMAGE}"
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
            sh "sudo docker image prune -f || true"
        }
    }

}

