pipeline {
    agent any

    environment {
        BACKEND_IMAGE = "jenkins-project-backend"
        FRONTEND_IMAGE = "jenkins-project-frontend"
        DOCKERHUB_REPO = "jenkins-project"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', 
                credentialsId: 'github-credentials', 
                url: 'https://github.com/SandyN12058/Jenkins-Project.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    sh "docker build -t $BACKEND_IMAGE:latest ./backend"
                    sh "docker build -t $FRONTEND_IMAGE:latest ./frontend"
                }
            }
        }

        stage('Push to DockerHub (Backup)') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                        
                        // tagging backend images
                        sh "docker tag $BACKEND_IMAGE:latest $DOCKER_USERNAME/$DOCKERHUB_REPO:backend-latest"
                        sh "docker tag $BACKEND_IMAGE:latest $DOCKER_USERNAME/$DOCKERHUB_REPO:backend-v${BUILD_NUMBER}"

                        // tagging frontend images
                        sh "docker tag $FRONTEND_IMAGE:latest $DOCKER_USERNAME/$DOCKERHUB_REPO:frontend-latest"
                        sh "docker tag $FRONTEND_IMAGE:latest $DOCKER_USERNAME/$DOCKERHUB_REPO:frontend-v${BUILD_NUMBER}"

                        // pushing backend images              
                        sh "docker push $DOCKER_USERNAME/$DOCKERHUB_REPO:backend-latest"
                        sh "docker push $DOCKER_USERNAME/$DOCKERHUB_REPO:backend-v${BUILD_NUMBER}"

                        // pushing frontend images
                        sh "docker push $DOCKER_USERNAME/$DOCKERHUB_REPO:frontend-latest"
                        sh "docker push $DOCKER_USERNAME/$DOCKERHUB_REPO:frontend-v${BUILD_NUMBER}"

                        // logout from docker
                        sh "docker logout"
                    }
                }
            }
        }

        stage('Deploy Containers') {
            steps {
                script {
                    sh "docker-compose up -d --remove-orphans"
                    sh "docker image prune -af --filter 'until=10m ago'"
                }
            }
        }
    }
}