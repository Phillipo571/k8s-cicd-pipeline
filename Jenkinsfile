pipeline {
    agent any
    
    environment {
        DOCKER_HUB_ID = 'phillip571'
        IMAGE_NAME = 'k8s-test-app'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Build Image') {
            steps {
                script {
                    echo "Building Docker Image..."
                    sh "docker build -t ${DOCKER_HUB_ID}/${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'DOCKER_PW', usernameVariable: 'DOCKER_ID')]) {
                        sh "echo \${DOCKER_PW} | docker login -u \${DOCKER_ID} --password-stdin"
                        sh "docker push ${DOCKER_HUB_ID}/${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }

        // 수정됨: stages 블록이 닫히기 전 안쪽에 위치
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo "Deploying to Kubernetes Cluster..."
                    sh "sed -i 's|phillip571/k8s-test-app:.*|phillip571/k8s-test-app:${IMAGE_TAG}|g' deployment.yaml"
                    sh "kubectl apply -f deployment.yaml"
                    sh "kubectl apply -f service.yaml"
                }
            }
        }
    } // <--- 전체 stages 블록이 끝나는 위치
}