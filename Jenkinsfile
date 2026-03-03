pipeline {
    agent any

    environment {
        IMAGE_NAME = "bkkrish007/spotify-devops"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/BK-KRISH/Spotify-Clone-DevOps.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:latest")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-creds') {
                        docker.image("${IMAGE_NAME}:latest").push()
                    }
                }
            }
        }
    }
}