pipeline {
    agent any

    environment {
        IMAGE_NAME = "saravananboopathykumar/flask-cicd"
        REGISTRY_CREDENTIAL = "dockerhub" // Jenkins DockerHub credentials ID
        KUBE_CONFIG = "/var/lib/jenkins/.kube/config" // Jenkins Kube config
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10')) // Keep last 10 builds
        timestamps() // Add timestamps to logs
    }

    stages {

        // 1️⃣ Checkout the code from Git
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        // 2️⃣ Run Python/Flask tests (if any)
        stage('Run Tests') {
            steps {
                script {
                    if (fileExists('tests/')) {
                        sh 'pip install pytest'
                        sh 'pytest tests/'
                    } else {
                        echo "No tests found, skipping."
                    }
                }
            }
        }

        // 3️⃣ Build Docker image
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        // 4️⃣ Push Docker image to Docker Hub
        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: "${REGISTRY_CREDENTIAL}", url: ""]) {
                    sh "docker push ${IMAGE_NAME}:latest"
                }
            }
        }

        // 5️⃣ Deploy to Kubernetes
        stage('Deploy to Kubernetes') {
            environment {
                KUBECONFIG = "${KUBE_CONFIG}"
            }
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }

        // 6️⃣ Verify deployment (optional)
        stage('Verify Deployment') {
            steps {
                environment {
                    KUBECONFIG = "${KUBE_CONFIG}"
                }
                sh '''
                echo "Waiting for pod to be ready..."
                kubectl rollout status deployment/flask-deployment --timeout=60s
                kubectl get pods -o wide
                kubectl get svc
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline finished successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs!"
        }
    }
}
}
