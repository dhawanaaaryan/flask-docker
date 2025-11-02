pipeline {
    agent any

    environment {
        IMAGE_NAME = "hello-flask"
        CONTAINER_NAME = "hello-flask-container"
        PORT = "5000"
    }

    stages {
        stage("Clone Code") {
            steps {
                echo "🔁 Cloning the code from GitHub..."
                git branch: "main", url: "https://github.com/dhawanaaaryan/task1.git"
            }
        }

        stage('Clean Old Containers & Images') {
            steps {
                echo "🧹 Removing old containers and images..."
                bat '''
                echo Listing containers...
                docker ps -a

                echo Removing containers...
                for /f "tokens=*" %%i in ('docker ps -aq') do docker rm -f %%i

                echo Removing images...
                for /f "tokens=*" %%i in ('docker images -q') do docker rmi -f %%i
                '''
            }
        }

        stage("Build Docker Image") {
            steps {
                echo "⚙ Building Docker image..."
                bat "docker build -t %IMAGE_NAME%:latest ."
            }
        }

        stage("Deploy Container") {
            steps {
                echo "🚀 Deploying container..."
                bat '''
                docker run -d --name %CONTAINER_NAME% -p %PORT%:5000 %IMAGE_NAME%:latest
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Application is running at http://localhost:${PORT}"
        }
        failure {
            echo "❌ Deployment failed! Check Jenkins logs for details."
        }
    }
}