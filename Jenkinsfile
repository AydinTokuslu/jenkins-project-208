pipeline {
    agent any
    environment {
        ECR_REGISTRY = "731967392843.dkr.ecr.us-east-1.amazonaws.com"
        APP_REPO_NAME= "clarusway/jenkins-project"
    }
    
    
    stages {
        stage('Checkout') {
            steps {
                // Checkout your source code repository
                checkout scm
            }
        }

        
        stage('Terraform Init') {
            steps {
                // Install and configure Terraform
                sh 'curl -LO https://releases.hashicorp.com/terraform/0.15.0/terraform_0.15.0_linux_amd64.zip'
                sh 'unzip terraform_0.15.0_linux_amd64.zip'
                sh 'chmod +x terraform'
                sh 'export PATH=$PATH:$PWD'
                
                // Initialize Terraform in your working directory
                sh 'terraform init'
            }
        }
        
        stage('Fetch Terraform File') {
            steps {
                // Fetch the Terraform file from GitHub
                sh 'curl -O https://raw.githubusercontent.com/AydinTokuslu/jenkins-project-208/main/main.tf'
            }
        }
        
        stage('Terraform Plan') {
            steps {
                // Generate Terraform plan
                sh 'terraform plan -out=tfplan'
            }
        }
        
        stage('Terraform Apply') {
            steps {
                // Apply the Terraform plan
                sh 'terraform apply -auto-approve tfplan'
            }
        }
        
        stage('Cleanup') {
            steps {
                // Clean up any temporary files
                sh 'rm -rf terraform* tfplan terraform-file.tf'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build --force-rm -t "$ECR_REGISTRY/$APP_REPO_NAME:latest" .'
                sh 'docker image ls'
            }
        }
        stage('Push Image to ECR Repo') {
            steps {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin "$ECR_REGISTRY"'
                sh 'docker push "$ECR_REGISTRY/$APP_REPO_NAME:latest"'
            }
        }
        stage('Deploy') {
            steps {
                sh 'aws ecs update-service --cluster to-do-app --desired-count 1 --service to-do-app-service --task-definition to-do-app --force-new-deployment'
                sh 'sleep 100'
            }
        }
        stage('Terraform Destroy') {
            steps {
                // Destroy the Terraform infrastructure
                sh 'terraform destroy -auto-approve'
                sh 'sleep 100'
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