pipeline {
    agent any
    environment {
        ECR_REGISTRY = "341162387145.dkr.ecr.ap-northeast-2.amazonaws.com"
        APP_REPO_NAME = "nsa"
        AWS_REGION = "ap-northeast-2"
    }
    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build --force-rm -t "$ECR_REGISTRY/$APP_REPO_NAME:latest" .'
                sh 'docker image ls'
            }
        }
        stage('Push Image to ECR Repo') {
            steps {
                sh '''
                    aws ecr get-login-password --region $AWS_REGION \
                    | docker login --username AWS --password-stdin $ECR_REGISTRY

                    docker push $ECR_REGISTRY/$APP_REPO_NAME:latest
                '''
            }
        }
    }
    post {
        always {
            echo '🧹 Deleting all local Docker images...'
            sh 'docker image prune -af'
        }
    }
}
