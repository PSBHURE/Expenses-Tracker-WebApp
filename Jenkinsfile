pipeline {
    agent { label "Terraform-Node"}

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
		stage('Backend Creation') {
			steps {
				dir('Terraform/bootstrap'){
					sh "terraform init"
					sh "terraform plan -out tfplan1"
					sh "terraform show -no-color tfplan1 > tfplan1.txt"
				}
			}		
		}
        stage('Terraform init') {
            steps {
				dir('Terraform'){
                sh "terraform init"
                sh "terraform plan -out tfplan"
                sh "terraform show -no-color tfplan > tfplan.txt"
                sh "terraform apply -auto-approve"
				}
            }
        }
    }
}
