pipeline {
    agent any

    environment {
        REGISTRY = "registry-vs.m-society.go.th:5050"
        IMAGE_NAME = "${REGISTRY}/kitsune-cop/test_html"
        TAG = "${GIT_COMMIT.take(8)}"
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
        NAMESPACE = "test-demo"
    }

    stages {

        stage('Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${TAG} ."
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'devop-bot',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh """
                        echo \$PASS | docker login ${REGISTRY} \
                        -u \$USER --password-stdin

                        docker push ${IMAGE_NAME}:${TAG}
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                sh """
                    kubectl -n ${NAMESPACE} set image deployment/html-demo \
                        nginx=${IMAGE_NAME}:${TAG}

                    kubectl -n ${NAMESPACE} rollout status deployment/html-demo
                """
            }
        }
    }
}