pipeline {
    agent { label "Terraform-Node" }

    parameters {
        choice(name: 'ACTION', choices: ['apply', 'destroy'], description: 'Select Terraform action to perform')
    }

    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_DEFAULT_REGION    = 'ap-south-1'
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                git branch: 'main', url: 'https://github.com/PSBHURE/Expenses-Tracker-WebApp.git'
            }
        }

        // ===========================
        // APPLY STAGES
        // ===========================
        stage('Terraform Apply') {
            when { expression { params.ACTION == 'apply' } }
            steps {
                script {
                    // Bootstrap: Create backend resources (S3 + DynamoDB)
                    dir('Terraform/bootstrap') {
                        sh "terraform init"
                        sh "terraform plan -out tfplan1"
                        sh "terraform show -no-color tfplan1 > tfplan1.txt"
                        sh "terraform apply -auto-approve"
                    }

                    // Main Infra
                    dir('Terraform') {
                        sh "terraform init"
                        sh "terraform plan -out tfplan"
                        sh "terraform show -no-color tfplan > tfplan.txt"
                        sh "terraform apply -auto-approve"
                    }
                }
            }
        }

        // ===========================
        // DESTROY STAGES
        // ===========================
        stage('Terraform Destroy') {
            when { expression { params.ACTION == 'destroy' } }
            steps {
                script {
                    // Destroy main infra first
                    dir('Terraform') {
                        sh "terraform init"
                        sh "terraform destroy -auto-approve"
                    }

                    // Now migrate backend state to local before destroying bootstrap
                    dir('Terraform/bootstrap') {
                        // Migrate away from S3/DynamoDB so Terraform can destroy them
                        sh "terraform init -migrate-state -backend=false"
                        sh "terraform destroy -auto-approve"
                    }
                }
            }
        }
    }
}
