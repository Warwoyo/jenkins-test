pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'ghcr.io/warwoyo'
        DOCKER_IMAGE = 'jenkins-test'
        DOCKER_TAG = "${BUILD_NUMBER}"
        KUBECONFIG_CREDENTIAL_ID = 'kubeconfig'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Warwoyo/jenkins-test.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG} .
                """
            }
        }

        stage('Push to Registry') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-credentials',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh """
                        echo $PASS | docker login ${DOCKER_REGISTRY} -u $USER --password-stdin
                        docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIAL_ID}", variable: 'KUBECONFIG_FILE')]) {
                    sh """
                        export KUBECONFIG=$KUBECONFIG_FILE
                        kubectl set image deployment/jenkins-test jenkins-test=${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG} -n default
                    """
                }
            }
        }
    }
}
