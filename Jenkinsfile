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

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKERHUB_USERNAME',
                    passwordVariable: 'DOCKERHUB_TOKEN'
                )]) {
                    bat 'echo %DOCKERHUB_TOKEN%| docker login -u "%DOCKERHUB_USERNAME%" --password-stdin'
                    bat 'docker tag devops-cicd-app:1.0 %DOCKERHUB_USERNAME%/devops-cicd-app:1.0'
                    bat 'docker push %DOCKERHUB_USERNAME%/devops-cicd-app:1.0'
                }
            }
        }

        stage('Test EC2 SSH') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    bat '''
                        ssh -o StrictHostKeyChecking=no ec2-user@3.88.204.178 "echo EC2 SSH connection successful && docker --version"
                    '''
                }
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
            bat 'docker stop devops-cicd-container 2>nul || exit /b 0'
            bat 'docker rm devops-cicd-container 2>nul || exit /b 0'
        }
    }
}