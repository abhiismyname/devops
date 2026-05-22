pipeline {
    agent any

    environment {
        IMAGE_NAME = "attendtrack"
        CONTAINER_NAME = "attendtrack-app"
        HOST_PORT = "3000"
        CONTAINER_PORT = "80"
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📥 Pulling latest code from GitHub...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                bat 'docker build -t %IMAGE_NAME%:latest .'
            }
        }

        stage('Stop Old Container') {
            steps {
                echo '🛑 Stopping and removing old container (if running)...'
                bat '''
                    docker stop %CONTAINER_NAME% || exit /b 0
                    docker rm %CONTAINER_NAME% || exit /b 0
                '''
            }
        }

        stage('Run New Container') {
            steps {
                echo '🚀 Starting new container on port 3000...'
                bat 'docker run -d --name %CONTAINER_NAME% -p %HOST_PORT%:%CONTAINER_PORT% --restart unless-stopped %IMAGE_NAME%:latest'
            }
        }

        stage('Health Check') {
            steps {
                echo '✅ Verifying container is running...'
                bat 'docker ps | findstr %CONTAINER_NAME%'
            }
        }
    }

    post {
        success {
            echo '🎉 Pipeline SUCCESS — AttendTrack is live at http://localhost:3000'
        }
        failure {
            echo '❌ Pipeline FAILED — check the logs above'
        }
        always {
            echo '🧹 Cleaning up dangling Docker images...'
            bat 'docker image prune -f || exit /b 0'
        }
    }
}
