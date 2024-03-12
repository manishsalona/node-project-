pipeline {
    agent {
        kubernetes {
            label 'my-kubernetes-pod-label'
            defaultContainer 'jnlp'
        }
    }
    environment {
        DOCKER_IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKER_IMAGE_NAME = "my-node-app:${DOCKER_IMAGE_TAG}"
        KUBE_NAMESPACE = "default"
    }
    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE_NAME} .'
            }
        }
         stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl apply -f deployment.yaml --namespace=${KUBE_NAMESPACE}"
                    sh "kubectl set image deployment/my-node-app my-node-app=${DOCKER_IMAGE_NAME} --namespace=${KUBE_NAMESPACE}"
                }
            }
        }
    }
    post {
        always {
            script {
                // Clean up Docker images after deployment
                sh "docker rmi ${DOCKER_IMAGE_NAME}"
            }
        }
    }
}
