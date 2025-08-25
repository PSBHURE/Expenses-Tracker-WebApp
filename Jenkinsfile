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

        // Normal execution order (Bootstrap first, then Main)
        stage('Terraform Apply') {
            when { expression { params.ACTION == 'apply' } }
            steps {
                script {
                    dir('Terraform/bootstrap') {
                        sh "terraform init"
                        sh "terraform plan -out tfplan1"
                        sh "terraform show -no-color tfplan1 > tfplan1.txt"
                        sh "terraform apply -auto-approve"
                    }
                    dir('Terraform') {
                        sh "terraform init"
                        sh "terraform plan -out tfplan"
                        sh "terraform show -no-color tfplan > tfplan.txt"
                        sh "terraform apply -auto-approve"
                    }
                }
            }
        }

        // Reverse execution order for destroy
        stage('Terraform Destroy') {
            when { expression { params.ACTION == 'destroy' } }
            steps {
                script {
                    dir('Terraform') {
                        sh "terraform init"
                        sh "terraform destroy -auto-approve"
                    }
                    dir('Terraform/bootstrap') {
                        sh "terraform init"
                        sh "terraform destroy -auto-approve"
                    }
                }
            }
        }
    }
}
