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
                    sh "docker build -t ${DOCKER_HUB_REPO}:${env.BUILD_NUMBER} -f app/Dockerfile app/"
                }
            }
        }
        
        
    //    stage('Build Docker Image') {
    //        steps {
    //            script {
    //                // Build Docker image using the Dockerfile inside the /app directory
    //                dockerImage = docker.build("${env.DOCKER_HUB_REPO}:${env.BUILD_NUMBER}", "app/")
    //            }
    //        }
    //    }
        
       stage('Push Docker Image') {
            steps {
                script {
                    // Push the built Docker image to Docker Hub with credentials
                    withDockerRegistry([credentialsId: 'game', url: '']) {
                         sh "docker push ${DOCKER_HUB_REPO}:${env.BUILD_NUMBER}"
                    }
                }
            }
        }

        stage('Configure AWS CLI') {
            steps {
                script {
                    // Use Jenkins credentials (id: 'cred') for AWS CLI configuration
                    withCredentials([awsCredentials(credentialsId: 'cred')]) {
                        // Configure AWS CLI with the provided credentials
                        sh '''
                        aws configure set aws_access_key_id ${AWS_ACCESS_KEY_ID}
                        aws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY}
                        aws configure set region eu-north-1 // Set your desired AWS region
                        '''
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Validate Kubernetes config and update deployment file
                    sh "kubectl get nodes"
                    // Update the Kubernetes deployment YAML with the new image tag
                    sh """
                    sed -i 's|image: ${DOCKER_HUB_REPO}:.*|image: ${DOCKER_HUB_REPO}:${env.BUILD_NUMBER}|' ${K8S_DEPLOY_DIR}/deployment.yaml
                    """
                    // Apply the namespace, deployment, and service YAML files to the EKS cluster
                    sh """
                    kubectl apply -f ${K8S_DEPLOY_DIR}/deployment.yaml
                    kubectl apply -f ${K8S_DEPLOY_DIR}/service.yaml 
                    """
                }
            }
        }
    }

    post {
  
        always {
            cleanWs()  // Clean workspace after build
        }
    }
}