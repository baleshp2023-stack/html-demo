pipeline {
    agent any
    environment {
        // REPLACE with your actual Docker Hub username
        DOCKER_IMAGE = "baleshp/html-demo:latest"
    }
    stages {
        // We removed 'Clone Code' because Jenkins does it automatically
        
        stage('Build Docker Image') {
            steps {
                bat "docker build -t %DOCKER_IMAGE% ."
            }
        }
        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat "docker login -u %USER% -p %PASS%"
                    bat "docker push %DOCKER_IMAGE%"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
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
