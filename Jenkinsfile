pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        DOCKER_HUB_USER = "poorans"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Code already checked"
            }
        }

        stage('Build Image') {
            steps {
                sh '''
                docker build -t $DOCKER_HUB_USER/$IMAGE_NAME:latest .
                '''
            }
        }

        stage('Login Docker') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                docker push $DOCKER_HUB_USER/$IMAGE_NAME:latest
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop myapp || true
                docker rm myapp || true
                docker run -d --name myapp -p 5000:5000 $DOCKER_HUB_USER/$IMAGE_NAME:latest
                '''
            }
        }
    }
}