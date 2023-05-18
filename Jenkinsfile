pipeline {
    agent any
    environment {
        ECR_REGISTRY = "731967392843.dkr.ecr.us-east-1.amazonaws.com"
        APP_REPO_NAME = "jenkinsproject"
    }
    stages {
        stage('Checkout') {
            steps {
                // Fetch the source code into the Jenkins workspace
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                // Change to the directory where the Dockerfile is located
                dir('path/to/Dockerfile/directory') {
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin "$ECR_REGISTRY"'
                    sh 'docker build --force-rm -t "$ECR_REGISTRY/$APP_REPO_NAME:latest" .'
                    sh 'docker image ls'
                }
            }
        }
        stage('Push Image to ECR Repo') {
            steps {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin "$ECR_REGISTRY"'
                sh 'docker push "$ECR_REGISTRY/$APP_REPO_NAME:latest"'
            }
        }
    }
    post {
        always {
            echo 'Deleting all local images'
            sh 'docker image prune -af'
        }
    }
}