pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                sh 'python3 --version'
                sh 'pytest -v'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t devops-cicd-app:1.0 .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker stop devops-cicd-app || true
                    docker rm devops-cicd-app || true
                    docker run -d --name devops-cicd-app -p 5000:5000 devops-cicd-app:1.0
                '''
            }
        }

        stage('Health Check') {
            steps {
                sleep 5
                sh 'curl -f http://localhost:5000/health'
            }
        }
    }
}
