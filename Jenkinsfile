pipeline {
    agent any

    environment {
		AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_DEFAULT_REGION    = 'ap-south-1'
    }

    stages {
		stage('Backend Creation') {
			steps {
				dir('bootstrap'){
					sh "terraform init"
					sh "terraform plan -out tfplan1"
					sh "terraform show -no-color tfplan1 > tfplan1.txt"
				}
			}		
		}
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/PSBHURE/Expenses-Tracker-WebApp.git'
            }
        }
        stage('Terraform init') {
            steps {
                sh "terraform init"
            }
        }
        stage('Plan') {
            steps {
                sh "terraform plan -out tfplan"
                sh "terraform show -no-color tfplan > tfplan.txt"
            }
        }
        stage('Apply') {
            steps {
                script {
                    sh "terraform apply -auto-approve"
                }
            }
        }

    }
}