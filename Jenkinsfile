pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'games-everywhere-flask'
        DOCKER_CONTAINER = 'games-station-container'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    try {
                        sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error "Failed to build Docker image: ${e.message}"
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Add your test commands here
                    // For example:
                    // sh 'docker run --rm ${DOCKER_IMAGE}:${BUILD_NUMBER} python -m pytest'
                    echo "Running tests... (placeholder)"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    try {
                        // Stop and remove existing container if it exists
                        sh 'docker stop ${DOCKER_CONTAINER} || true'
                        sh 'docker rm ${DOCKER_CONTAINER} || true'
                        
                        // Run the new container
                        sh 'docker run -d --name ${DOCKER_CONTAINER} -p 8080:8080 ${DOCKER_IMAGE}:${BUILD_NUMBER}'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error "Failed to deploy Docker container: ${e.message}"
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up old images
            sh 'docker image prune -f'
        }
        failure {
            // Additional failure handling if needed
            echo 'The Pipeline failed :('
        }
        success {
            echo 'The Pipeline completed successfully :)'
        }
    }
}