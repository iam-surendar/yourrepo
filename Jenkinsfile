pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/iam-surendar/yourrepo.git'
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'AWS_CREDS'
                ]]) {
                    sh '''
                        aws ecr get-login-password --region ap-south-1 | \
                        docker login --username AWS --password-stdin 118162274565.dkr.ecr.ap-south-1.amazonaws.com
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t calculator-image .
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                    docker tag calculator-image:latest 118162274565.dkr.ecr.ap-south-1.amazonaws.com/calculator-repo:latest
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                    docker push 118162274565.dkr.ecr.ap-south-1.amazonaws.com/calculator-repo:latest
                '''
            }
        }
    }
}
