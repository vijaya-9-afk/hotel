pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'vijaya9494'
        IMAGE_NAME = 'vijaya9494/hotel'
        TAG = 'v1'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/vijaya-9-afk/hotel.git'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${TAG} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKERHUB_CREDENTIALS}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker push ${IMAGE_NAME}:${TAG}
                        docker logout
                    """
                }
            }
        }

        stage('Run Container') {
            steps {
                sh """
                    docker stop restaurant || true
                    docker rm restaurant || true
                    docker run -d -p 4444:8080 --name restaurant ${IMAGE_NAME}:${TAG}
                """
            }
        }
    }
}
