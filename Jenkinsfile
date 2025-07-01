pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/soumyapatil02/FAST-api-hello-world.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("fastapi-app:${env.BUILD_ID}")
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    sh '''
                        docker stop fastapi-container || true
                        docker rm fastapi-container || true
                        docker run -d --name fastapi-container -p 8000:8000 fastapi-app:${BUILD_ID}
                    '''
                }
            }
        }
    }
}