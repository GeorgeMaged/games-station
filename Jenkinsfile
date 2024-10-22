pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'gogomaged/snake-game'  // Your Docker Hub repository
        GIT_REPO_URL = 'https://github.com/GeorgeMaged/games-station.git'  // Your public GitHub repository URL
        KUBECONFIG_PATH = '/var/lib/jenkins/.kube/config'  // Path to kubeconfig on Jenkins server
        K8S_DEPLOY_DIR = 'k8s/'  // Directory containing Kubernetes YAML files
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Clone the Git repository
                git branch: 'master', url: "${env.GIT_REPO_URL}"
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image using the Dockerfile inside the /app directory
                    dockerImage = docker.build("${env.DOCKER_HUB_REPO}:${env.BUILD_NUMBER}", "app/")
                }
            }
        }
        
       stage('Push Docker Image') {
            steps {
                script {
                    // Push the built Docker image to Docker Hub with credentials
                    docker.withRegistry('https://index.docker.io/v1/', 'game') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Update the Kubernetes deployment YAML with the new image tag
                    sh """
                    sed -i 's|image: ${DOCKER_HUB_REPO}:.*|image: ${DOCKER_HUB_REPO}:${env.BUILD_NUMBER}|' ${K8S_DEPLOY_DIR}/deployment.yaml
                    """
                    // Apply the namespace, deployment, and service YAML files to the EKS cluster
                    sh """
                    kubectl --kubeconfig ${env.KUBECONFIG_PATH} apply -f ${K8S_DEPLOY_DIR}/deployment.yaml
                    kubectl --kubeconfig ${env.KUBECONFIG_PATH} apply -f ${K8S_DEPLOY_DIR}/service.yaml 
                    """
                }
            }
        }
    }

    post {
        success {
            slackSend(channel: '#devops-project', color: 'good', message: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' succeeded.")
        }
        failure {
            slackSend(channel: '#devops-project', color: 'danger', message: "FAILURE: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' failed.")
        }
        always {
            cleanWs()  // Clean workspace after build
        }
    }
}