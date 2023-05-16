pipeline {
    agent any
    
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
    }
}