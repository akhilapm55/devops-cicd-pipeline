
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

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    bat '''
                        ssh -o StrictHostKeyChecking=no ec2-user@3.88.204.178 "docker pull akhilapm/devops-cicd-app:1.0 && docker stop devops-cicd-container || true && docker rm devops-cicd-container || true && docker run -d --name devops-cicd-container -p 5000:5000 akhilapm/devops-cicd-app:1.0"
                    '''
                }
            }
        }

        stage('Health Check') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    bat '''
                        ssh -o StrictHostKeyChecking=no ec2-user@3.88.204.178 "sleep 5 && curl -f http://localhost:5000/health"
                    '''
                }
            }
        }
    }
}

