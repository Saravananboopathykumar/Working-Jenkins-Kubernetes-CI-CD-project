pipeline {
    agent any

    environment {
        IMAGE = "saravananboopathykumar/flask-cicd"
        TAG = "${env.BUILD_NUMBER}" // unique tag for each build
    }

    stages {
        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE:$TAG .'
            }
        }

        stage('Push Image') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub', url: '']) {
                    sh 'docker push $IMAGE:$TAG'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                sed -i 's|image: $IMAGE:.*|image: $IMAGE:$TAG|' deployment.yaml
                kubectl apply -f deployment.yaml
                """
            }
        }
    }
}
