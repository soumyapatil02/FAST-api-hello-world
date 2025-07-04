pipeline {
    agent any
    
    environment {
        IMAGE_NAME = "fastapi-app"
        CONTAINER_NAME = "fastapi-container"
        PORT = "8000"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/soumyapatil02/FAST-api-hello-world.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    def image = docker.build("${IMAGE_NAME}:${env.BUILD_ID}")
                    echo "Docker image built successfully"
                }
            }
        }
        
        stage('Test') {
            steps {
                script {
                    echo "Running basic container test..."
                    sh """
                        docker run --rm ${IMAGE_NAME}:${env.BUILD_ID} python -c "
import sys
sys.path.append('/app')
from main import app
print('FastAPI app imported successfully')
"
                    """
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    echo "Deploying application..."
                    sh """
                        # Stop and remove existing container if it exists
                        docker stop ${CONTAINER_NAME} || true
                        docker rm ${CONTAINER_NAME} || true
                        
                        # Run new container
                        docker run -d --name ${CONTAINER_NAME} -p ${PORT}:${PORT} ${IMAGE_NAME}:${env.BUILD_ID}
                        
                        # Wait a moment for container to start
                        sleep 10
                        
                        # Verify deployment
                        curl -f http://localhost:${PORT}/health || exit 1
                        echo "Application deployed successfully and health check passed"
                    """
                }
            }
        }
        
        stage('Cleanup Old Images') {
            steps {
                script {
                    echo "Cleaning up old Docker images..."
                    sh """
                        # Keep only the last 3 builds
                        docker images ${IMAGE_NAME} --format "table {{.Tag}}" | grep -E '^[0-9]+\$' | sort -nr | tail -n +4 | xargs -r docker rmi ${IMAGE_NAME}: || true
                    """
                }
            }
        }
    }
    
    post {
        always {
            echo "Pipeline completed"
        }
        success {
            echo "Deployment successful! Application is running at http://your-ec2-ip:8000"
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
