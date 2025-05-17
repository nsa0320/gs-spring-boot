pipeline {
    agent any
    tools {
        gradle 'gradel'  // Jenkins에 등록한 Gradle 도구 이름
    }
    environment {
        AWS_REGION = 'ap-northeast-2'
        ECR_REPO = '341162387145.dkr.ecr.ap-northeast-2.amazonaws.com/nsa'
        IMAGE_TAG = 'latest'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build with Gradle') {
            steps {
                sh 'gradle build -x test'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${ECR_REPO}:${IMAGE_TAG}")
                }
            }
        }
        stage('Push to ECR') {
            steps {
                script {
                    sh '''
                    aws ecr get-login-password --region $AWS_REGION \
                    | docker login --username AWS --password-stdin $ECR_REPO
                    docker push $ECR_REPO:$IMAGE_TAG
                    '''
                }
            }
        }
    }
    post {
        success {
            echo '✅ ECR 푸시 완료!'
        }
        failure {
            echo '❌ 실패함. 로그 확인.'
        }
    }
}

