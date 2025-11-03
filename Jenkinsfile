pipeline {
    agent any
    
    triggers {
        githubPush()
    }


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

        stage("Clean Old Containers & Images") {
            steps {
                echo "🧹 Removing old containers and images..."
                bat '''
                for /f "tokens=*" %%i in ('docker ps -aq') do docker rm -f %%i
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

        stage("Run Tests with pytest") {
            steps {
                echo "🧪 Running pytest inside a temporary container..."
                bat '''
                docker run --rm %IMAGE_NAME%:latest pytest --maxfail=1 --disable-warnings -q
                '''
            }
        }

        stage("Deploy Container") {
            steps {
                echo "🚀 Deploying Flask container..."
                bat '''
                docker run -d --name %CONTAINER_NAME% -p %PORT%:5000 %IMAGE_NAME%:latest
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Build & Deployment Successful! App running at http://localhost:${PORT}"
        }
        failure {
            echo "❌ Build Failed! Check Jenkins logs for error details."
        }
    }
}
