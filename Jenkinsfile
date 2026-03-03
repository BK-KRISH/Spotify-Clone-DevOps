pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "bkkrish007/spotify-devops:v2"
        EC2_IP = "13.235.179.251"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/BK-KRISH/Spotify-Clone-DevOps.git'
            }
        }

        stage('Build & Push (AMD64)') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker buildx create --name multiarch --driver docker-container --use || true
                    docker buildx inspect --bootstrap
                    docker buildx build \
                      --platform linux/amd64 \
                      -t $DOCKER_IMAGE \
                      --push .
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} '
                    docker stop spotify || true
                    docker rm spotify || true
                    docker pull ${DOCKER_IMAGE}
                    docker run -d -p 8081:80 --name spotify ${DOCKER_IMAGE}
                    '
                    """
                }
            }
        }
    }
}