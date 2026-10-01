pipeline {
    agent any
    
    environment {
        // 본인의 Docker Hub 계정 Username으로 반드시 변경!
        DOCKER_HUB_ID = 'kylee413@naver.com'
        IMAGE_NAME = 'k8s-test-app'
        IMAGE_TAG = "${env.BUILD_NUMBER}" // Jenkins 빌드 번호를 태그(버전)로 자동 사용
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
                    // 자격 증명 등록 시 만든 ID('docker-hub-creds')를 호출하여 로그인
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'DOCKER_PW', usernameVariable: 'DOCKER_ID')]) {
                        sh "echo \${DOCKER_PW} | docker login -u \${DOCKER_ID} --password-stdin"
                        sh "docker push ${DOCKER_HUB_ID}/${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }
    }
}