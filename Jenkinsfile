pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                bat 'python --version'
                bat 'pytest -v'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t devops-cicd-app:1.0 .'
            }
        }

        stage('Docker Run') {
            steps {
                bat 'docker run -d --name devops-cicd-container -p 5000:5000 devops-cicd-app:1.0'
            }
        }

        stage('Health Check') {
            steps {
                bat 'powershell -Command "Start-Sleep -Seconds 5; $response = Invoke-WebRequest -Uri http://localhost:5000/health -UseBasicParsing; if ($response.StatusCode -ne 200) { exit 1 }"'
            }
        }
    }

    post {
        always {
            bat 'docker stop devops-cicd-container'
            bat 'docker rm devops-cicd-container'
        }
    }
}