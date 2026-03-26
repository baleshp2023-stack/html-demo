pipeline {
    agent any
    environment {
        // Change 'YOUR_DOCKERHUB_USERNAME' to your actual Docker Hub ID
        DOCKER_IMAGE = "YOUR_DOCKERHUB_USERNAME/html-demo:latest"
    }
    stages {
        stage('Clone Code') {
            steps {
                // I have updated the URL to your specific GitHub repository below
                git branch: 'main', url: 'https://github.com'
            }
        }
        stage('Build Docker Image') {
            steps {
                // Using "bat" because your Jenkins is running on Windows
                bat "docker build -t %DOCKER_IMAGE% ."
            }
        }
        stage('Push Image') {
            steps {
                // This looks for a Jenkins Credential with ID 'dockerhub'
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat "docker login -u %USER% -p %PASS%"
                    bat "docker push %DOCKER_IMAGE%"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                // This looks for a Jenkins 'Secret File' Credential with ID 'kuberconfig'
                withCredentials([file(credentialsId: 'kuberconfig', variable: 'KUBECONFIG_PATH')]) {
                    bat """
                    set KUBECONFIG=%KUBECONFIG_PATH%
                    kubectl apply -f deployment.yaml --validate=false
                    """
                }
            }
        }
    }
}
