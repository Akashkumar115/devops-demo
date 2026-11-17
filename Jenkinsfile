pipeline {
    agent any

    environment {
        IMAGE = "akash7835/devops-demo"
        DOCKER_CREDS = credentials('dockerhub-creds')
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Install & Test') {
            steps {
                bat 'npm install'
                bat 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE%:%BUILD_NUMBER% .'
                bat 'docker tag %IMAGE%:%BUILD_NUMBER% %IMAGE%:latest'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                bat 'echo %DOCKER_CREDS_PSW%| docker login -u %DOCKER_CREDS_USR% --password-stdin'
                bat 'docker push %IMAGE%:%BUILD_NUMBER%'
                bat 'docker push %IMAGE%:latest'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f k8s/'
                bat 'kubectl set image deployment/devops-demo devops-demo=%IMAGE%:%BUILD_NUMBER%'
                bat 'kubectl rollout status deployment/devops-demo'
            }
        }
    }

    post {
        success { echo 'Deployed successfully!' }
        failure { echo 'Pipeline failed.' }
    }
}
