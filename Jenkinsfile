pipeline {
    agent any
    docker {
            image 'docker:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    environment {
        DOCKER_IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKER_IMAGE_NAME = "my-node-app:${DOCKER_IMAGE_TAG}"
        KUBE_NAMESPACE = "default"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE_NAME)
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl apply -f deployment.yaml"
                    sh "kubectl set image deployment/my-node-app my-node-app=${DOCKER_IMAGE_NAME} -n ${KUBE_NAMESPACE}"
                }
            }
        }
    }

    post {
        always {
            script {
                // Clean up Docker images after deployment
                docker.image(DOCKER_IMAGE_NAME).remove(force: true)
            }
        }
    }
}

