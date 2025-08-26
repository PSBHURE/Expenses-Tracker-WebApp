pipeline {
    agent { label "Main-Node" }

    stages {
        stage("Code") {
            steps {
                echo "Cloning the code..."
                cleanWs()
                git branch: 'main', url: 'https://github.com/PSBHURE/Expenses-Tracker-WebApp.git'
            }
        }

        stage("Build") {
            steps {
                echo "Building the code..."
                sh "whoami"
                dir("Expenses-Tracker-WebApp") {
                    sh "docker build -t expense-tracker-app:latest ."
                }
            }
        }

        stage("Push to Dockerhub") {
            steps {
                echo "Pushing Docker image to DockerHub..."

                script {
                    withCredentials([usernamePassword(
                        credentialsId: "DockerHubCred",
                        usernameVariable: "DockerHubUser",
                        passwordVariable: "DockerHubPass"
                    )]) {
                        sh '''
                            docker login -u $DockerHubUser -p $DockerHubPass
                            docker tag expense-tracker-app:latest $DockerHubUser/expense-tracker-app:latest
                            docker push $DockerHubUser/expense-tracker-app:latest
                        '''
                    }
                }
            }
        }

        stage("Deploy") {
            steps {
                echo "Deploying the code..."

                // Optional: stop any previous containers
                sh "docker compose down || true"

                // Start the containers
                sh "docker compose up -d"

                echo "Application is up and running."
            }
        }

        stage("Feedback") {
            steps {
                echo "Jenkins pipeline ran successfully! ✅"
            }
        }
    }
}
