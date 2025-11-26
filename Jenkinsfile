pipeline {
    agent any
    
    environment {
        // Replace with your Docker Hub username and roll number
        DOCKERHUB_USERNAME = 'dockeraisrh12'
        IMAGE_NAME = 'rollno-todo-cli'
        IMAGE_TAG = "${BUILD_NUMBER}"

        // Correct credential IDs from your screenshot
        GITHUB_CREDS = credentials('Github-JenkinCreds')
        DOCKERHUB_CREDS = credentials('Docker-Jenkins')

        // FULL PATH to Python (this fixes your entire pipeline)
        PYTHON = 'C:\\Users\\Aishwarya Sharma\\AppData\\Local\\Programs\\Python\\Python313\\python.exe'
    }
    
    stages {

        stage('Checkout') {
            steps {
                echo "Pulling code from GitHub..."
                git branch: 'main',
                    url: 'https://github.com/Aishrma/ToDo-List-CLI-App.git',
                    credentialsId: 'Github-JenkinCreds'
            }
        }

        stage('Build - Install Dependencies') {
            steps {
                echo "Setting up Python virtual environment..."
                bat """
                    "%PYTHON%" -m venv venv
                    venv\\Scripts\\pip install --upgrade pip
                    venv\\Scripts\\pip install -r requirements.txt
                """
            }
        }

        stage('Run Tests') {
            steps {
                echo "Running Python tests..."
                bat """
                    venv\\Scripts\\pytest tests\\test_todo.py -v
                """
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                bat "docker build -t %DOCKERHUB_USERNAME%/%IMAGE_NAME%:%IMAGE_TAG% ."
                bat "docker build -t %DOCKERHUB_USERNAME%/%IMAGE_NAME%:latest ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Logging into Docker Hub & pushing image..."
                bat "docker login -u %DOCKERHUB_CREDS_USR% -p %DOCKERHUB_CREDS_PSW%"
                bat "docker push %DOCKERHUB_USERNAME%/%IMAGE_NAME%:%IMAGE_TAG%"
                bat "docker push %DOCKERHUB_USERNAME%/%IMAGE_NAME%:latest"
                bat "docker logout"
            }
        }

        stage('Verify Docker Image') {
            steps {
                echo "Verifying Docker image..."
                bat "docker images | findstr %IMAGE_NAME%"
            }
        }
    }

    post {
        success {
            echo "✓ Pipeline completed successfully!"
            echo "Docker image pushed: %DOCKERHUB_USERNAME%/%IMAGE_NAME%:%IMAGE_TAG%"
        }
        failure {
            echo "✗ Pipeline failed!"
        }
        always {
            echo "Cleaning workspace..."
            cleanWs()
        }
    }
}
