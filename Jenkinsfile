pipeline {
    agent any
    stages {
        stage('Clone') {
            steps { sh 'echo Cloned from https://github.com/javintrivedi/devops-lab-7' }
        }
        stage('Build Docker Image') {
            steps { sh 'echo docker build -t myapp .' }
        }
        stage('Deploy to K8s') {
            steps { sh 'echo kubectl apply -f deployment.yaml' }
        }
    }
}
