pipeline {
    agent any

    environment {
        ECR_REGISTRY = "118162274565.dkr.ecr.ap-south-1.amazonaws.com"
        ECR_REPO = "calculator-repo"
        IMAGE_TAG = "latest"
        AWS_REGION = "ap-south-1"
        AWS_CREDS = credentials('aws-creds')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/iam-surendar/yourrepo.git'
            }
        }

        stage('Login to ECR') {
    steps {
        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'AWS_CREDS']]) {
            sh '''
            aws ecr get-login-password --region ap-south-1 \
            | docker login --username AWS --password-stdin 118162274565.dkr.ecr.ap-south-1.amazonaws.com
            '''
        }
    }
}

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $ECR_REPO:$IMAGE_TAG ."
            }
        }

        stage('Tag Image') {
            steps {
                sh "docker tag $ECR_REPO:$IMAGE_TAG $ECR_REGISTRY/$ECR_REPO:$IMAGE_TAG"
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh "docker push $ECR_REGISTRY/$ECR_REPO:$IMAGE_TAG"
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh '''
                aws ecs update-service \
                  --cluster calculator-cluster \
                  --service calculator-service \
                  --force-new-deployment
                '''
            }
        }
    }
}
