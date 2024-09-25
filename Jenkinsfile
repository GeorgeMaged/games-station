pipeline {
    agent any 


    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/GeorgeMaged/games-station.git'
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