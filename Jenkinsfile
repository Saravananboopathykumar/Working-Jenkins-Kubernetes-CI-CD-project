pipeline {
    agent any

    environment {
        IMAGE = "saravananboopathykumar/flask-cicd" // Docker image name
        K8S_DEPLOYMENT_FILE = "deployment.yaml"   // Kubernetes deployment YAML
        DOCKER_CREDENTIALS_ID = "dockerhub"       // Jenkins Docker credentials ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Pull latest code from GitHub
                git branch: 'main', url: 'https://github.com/Saravananboopathykumar/Working-Jenkins-Kubernetes-CI-CD-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE}:latest ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry([credentialsId: "${DOCKER_CREDENTIALS_ID}", url: '']) {
                        sh "docker push ${IMAGE}:latest"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Apply the Kubernetes deployment YAML
                    sh "kubectl apply -f ${K8S_DEPLOYMENT_FILE}"
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    // Check pod status
                    sh "kubectl get pods -o wide"
                    // Optional: curl the service URL (update NodePort if needed)
                    sh "kubectl get svc"
                }
            }
        }
    }

    post {
        success {
            echo "🚀 CI/CD Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Check logs above."
        }
    }
}
