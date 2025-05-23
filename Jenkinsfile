pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'ghcr.io/yourusername'
        DOCKER_IMAGE = 'jenkins-test'
        DOCKER_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/yourusername/jenkins-test.git'
            }
        }

        stage('Build Image') {
            steps {
                sh """
                docker build -t ER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG} .
                """
            }
        }ush Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh """
                    echo $PASS | docker login ${DOCKER_REGISTRY} -u $USER --password-stdin
                    docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }

        
