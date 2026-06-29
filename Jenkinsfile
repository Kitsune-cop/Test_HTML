pipeline {

    agent any

    environment {
        IMAGE_NAME = "registry.example.com/demo/html-demo"
        TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://git.example.com/demo/html-demo.git'
            }
        }

        stage('Build Image') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME}:${TAG} .
                docker tag ${IMAGE_NAME}:${TAG} ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Login Registry') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-registry',
                        usernameVariable: 'USERNAME',
                        passwordVariable: 'PASSWORD'
                    )
                ]) {
                    sh """
                    echo \$PASSWORD | docker login registry.example.com \
                        -u \$USERNAME \
                        --password-stdin
                    """
                }
            }
        }

        stage('Push Image') {
            steps {
                sh """
                docker push ${IMAGE_NAME}:${TAG}
                docker push ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Deploy') {
            steps {
                sh """
                kubectl set image deployment/html-demo \
                    nginx=${IMAGE_NAME}:${TAG}

                kubectl rollout status deployment/html-demo
                """
            }
        }

    }

}