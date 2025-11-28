pipeline {
    agent any

    environment {
        DOCKERHUB = credentials('dockerhub-login')
        DOCKERHUB_USERNAME = "karmanghat"

        BACKEND_IMAGE = "${DOCKERHUB_USERNAME}/mean-backend:latest"
        FRONTEND_IMAGE = "${DOCKERHUB_USERNAME}/mean-frontend:latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/diwakarnaik219/crud-dd-task-mean-app.git'
            }
        }

        stage('Build Backend Image') {
            steps {
                sh '''
                    cd backend
                    docker build -t ${BACKEND_IMAGE} .
                '''
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh '''
                    cd frontend
                    docker build -t ${FRONTEND_IMAGE} .
                '''
            }
        }

        stage('Login to Docker Hub & Push Images') {
            steps {
                sh '''
                    echo "${DOCKERHUB_PSW}" | docker login -u "${DOCKERHUB_USR}" --password-stdin
                    docker push ${BACKEND_IMAGE}
                    docker push ${FRONTEND_IMAGE}
                '''
            }
        }

        stage('Deploy using Docker Compose') {
            steps {
                sh '''
                    echo "🔥 Stopping old containers..."
                    
                    # Change this to your folder where docker-compose.yml exists
                    cd /var/lib/jenkins/workspace/naik  
                    
                    docker compose down --remove-orphans || true

                    echo "📥 Pulling latest images..."
                    docker compose pull

                    echo "🚀 Starting updated containers..."
                    docker compose up -d --force-recreate

                    echo "🎉 Deployment successful!"
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline finished successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Check the logs."
        }
    }
}
