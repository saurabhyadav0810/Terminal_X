
pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'

        ECR_REGISTRY = '091746542059.dkr.ecr.ap-south-1.amazonaws.com'
        ECR_REPO = '091746542059.dkr.ecr.ap-south-1.amazonaws.com/terminalx'

        IMAGE_TAG = "${BUILD_NUMBER}"

        CLUSTER_NAME = 'terminalx-cluster'
        SERVICE_NAME = 'terminalx-server'
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/saurabhyadav0810/Terminal_X.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                      -t $ECR_REPO:latest \
                      -t $ECR_REPO:$IMAGE_TAG .
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                    aws ecr get-login-password \
                      --region $AWS_REGION | \
                    docker login \
                      --username AWS \
                      --password-stdin $ECR_REGISTRY
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                    docker push $ECR_REPO:latest
                    docker push $ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh '''
                    aws ecs update-service \
                      --cluster $CLUSTER_NAME \
                      --service $SERVICE_NAME \
                      --force-new-deployment \
                      --region $AWS_REGION
                '''
            }
        }
    }

    post {
        success {
            echo 'Terminal_X successfully deployed!'
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}
