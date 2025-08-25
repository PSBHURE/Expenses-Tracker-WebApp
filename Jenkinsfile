pipeline {
    agent { label "Terraform-Node" }

    parameters {
        choice(
            name: 'ACTION',
            choices: ['apply', 'destroy'],
            description: 'Choose whether to Apply or Destroy Terraform infrastructure'
        )
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

        stage('Terraform Apply Flow') {
            when {
                expression { params.ACTION == 'apply' }
            }
            stages {
                stage('Backend Creation') {
                    steps {
                        dir('Terraform/bootstrap') {
                            sh "terraform init"
                            sh "terraform plan -out tfplan1"
                            sh "terraform show -no-color tfplan1 > tfplan1.txt"
                            sh "terraform apply -auto-approve"
                        }
                    }
                }
                stage('Terraform Apply') {
                    steps {
                        dir('Terraform') {
                            sh "terraform init -reconfigure"
                            sh "terraform plan -out tfplan"
                            sh "terraform show -no-color tfplan > tfplan.txt"
                            sh "terraform apply -auto-approve"
                        }
                    }
                }
            }
        }

        stage('Terraform Destroy Flow') {
            when {
                expression { params.ACTION == 'destroy' }
            }
            stages {
                stage('Terraform Destroy') {
                    steps {
                        dir('Terraform') {
                            sh "terraform init -reconfigure"
                            sh "terraform destroy -auto-approve"
                        }
                    }
                }
                stage('Backend Destroy') {
                    steps {
                        dir('Terraform/bootstrap') {
                            sh "terraform init -reconfigure"
                            sh "terraform destroy -auto-approve"
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ Terraform pipeline finished successfully!"
        }
        failure {
            echo "❌ Terraform pipeline failed!"
        }
        always {
            cleanWs()
        }
    }
}
