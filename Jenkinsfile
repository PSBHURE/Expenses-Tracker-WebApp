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
                        sh """
                            terraform init
                            terraform plan -out tfplan1
                            terraform show -no-color tfplan1 > tfplan1.txt
                            terraform apply -auto-approve
                        """
                    }

                    // Main Infra (uses S3 + DynamoDB as backend)
                    dir('Terraform') {
                        sh """
                            terraform init
                            terraform plan -out tfplan
                            terraform show -no-color tfplan > tfplan.txt
                            terraform apply -auto-approve
                        """
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
                    // Step 1: Destroy main infra (still using remote backend)
                    dir('Terraform') {
                        sh """
                            terraform init
                            terraform destroy -auto-approve || echo "Nothing to destroy in main"
                        """
                    }

                    // Step 2: Switch bootstrap backend to local
                    dir('Terraform/bootstrap') {
                        sh """
                            terraform init -backend=false
                            terraform destroy -auto-approve || echo "Nothing to destroy in bootstrap"
                        """
                    }
                }
            }
        }
    }
}
