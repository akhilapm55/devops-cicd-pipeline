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
    }
}