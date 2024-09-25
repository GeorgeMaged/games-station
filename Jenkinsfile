pipeline {
    agent any 


    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'docker build -t games-everywhere-flask .'
                }
            }
        }

      /*  stage('Test') {
            steps {
                script {
                    //
                }
            }
        }*/

        stage('Deploy') {
            steps {
                script {
                    sh 'docker run -d -p 8080:8080 games-everywhere-flask'
                }
            }
        }
    }
}