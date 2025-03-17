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
                script {
                    echo "📥 Cloning Git repository..."
                    git branch: 'main', 
                        credentialsId: 'github-credentials', 
                        url: 'https://github.com/SandyN12058/Jenkins-Project.git'
                    echo "✅ Git repository cloned successfully."
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    echo "🚀 Building backend image..."
                    sh "docker build -t $BACKEND_IMAGE:latest ./backend"
                    echo "✅ Backend image built successfully."

                    echo "🚀 Building frontend image..."
                    sh "docker build -t $FRONTEND_IMAGE:latest ./frontend"
                    echo "✅ Frontend image built successfully."
                }
            }
        }

        stage('Push to DockerHub (Backup)') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        echo "🔐 Logging into DockerHub..."
                        sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                        echo "✅ Docker login successful."

                        echo "🏷️ Tagging backend images..."
                        sh "docker tag $BACKEND_IMAGE:latest $DOCKER_USERNAME/$DOCKERHUB_REPO:backend-latest"
                        sh "docker tag $BACKEND_IMAGE:latest $DOCKER_USERNAME/$DOCKERHUB_REPO:backend-v${BUILD_NUMBER}"
                        echo "✅ Backend images tagged successfully."

                        echo "🏷️ Tagging frontend images..."
                        sh "docker tag $FRONTEND_IMAGE:latest $DOCKER_USERNAME/$DOCKERHUB_REPO:frontend-latest"
                        sh "docker tag $FRONTEND_IMAGE:latest $DOCKER_USERNAME/$DOCKERHUB_REPO:frontend-v${BUILD_NUMBER}"
                        echo "✅ Frontend images tagged successfully."

                        echo "📤 Pushing backend images to DockerHub..."
                        sh "docker push $DOCKER_USERNAME/$DOCKERHUB_REPO:backend-latest"
                        sh "docker push $DOCKER_USERNAME/$DOCKERHUB_REPO:backend-v${BUILD_NUMBER}"
                        echo "✅ Backend images pushed successfully."

                        echo "📤 Pushing frontend images to DockerHub..."
                        sh "docker push $DOCKER_USERNAME/$DOCKERHUB_REPO:frontend-latest"
                        sh "docker push $DOCKER_USERNAME/$DOCKERHUB_REPO:frontend-v${BUILD_NUMBER}"
                        echo "✅ Frontend images pushed successfully."

                        echo "🚪 Logging out from DockerHub..."
                        sh "docker logout"
                        echo "✅ Logged out from Docker."
                    }
                }
            }
        }

        stage('Deploy Containers') {
            steps {
                script {
                    echo "🚀 Deploying containers..."
                    sh "docker-compose up -d --remove-orphans"
                    echo "✅ Deployment successful."

                    echo "🧹 Cleaning up old images..."
                    sh "docker image prune -af --filter 'until=10m ago'"
                    echo "✅ Image cleanup completed."
                }
            }
        }
    }
}
