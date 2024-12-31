pipeline {
    agent any
    environment {
        DOCKER_REGISTRY = 'docker.io' // Replace with your registry, e.g., ghcr.io for GitHub Container Registry
        IMAGE_NAME = 'nithiesh0609/petclinicapp' // Replace with your Docker image name
        IMAGE_TAG = 'v1' // Replace with your desired tag
        RENDER_SERVICE_ID = 'srv-ctpngbd2ng1s73dt55u0' // Replace with your Render service ID
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        sh """
                        echo "${DOCKER_PASS}" | docker login ${DOCKER_REGISTRY} -u "${DOCKER_USER}" --password-stdin
                        docker push ${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }
        stage('Deploy to Render') {
            steps {
                withCredentials([string(credentialsId: 'render-api-key', variable: 'RENDER_API_KEY')]) {
                    script {
                        sh """
                        curl -X POST \
                            -H "Authorization: Bearer ${RENDER_API_KEY}" \
                            -H "Content-Type: application/json" \
                            https://api.render.com/v1/services/${RENDER_SERVICE_ID}/deploys?imageUrl=${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }
    }
}
