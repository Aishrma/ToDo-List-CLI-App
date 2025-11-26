pipeline {
    agent any
    
    environment {
        // Replace with your Docker Hub username (your roll number)
        DOCKERHUB_USERNAME = 'your_roll_number'
        IMAGE_NAME = 'todo-app'
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Pulling code from GitHub...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building the application...'
                script {
                    if (isUnix()) {
                        sh 'python3 --version'
                        sh 'ls -la'
                    } else {
                        bat 'python --version'
                        bat 'dir'
                    }
                }
                echo 'Build completed successfully!'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                script {
                    if (isUnix()) {
                        sh 'python3 -m pytest tests/test_todo.py -v || python3 -m unittest discover -s tests -p "test_*.py" -v'
                    } else {
                        bat '''
                            python -m pytest tests/test_todo.py -v 2>nul || python -m unittest discover -s tests -p "test_*.py" -v
                        '''
                    }
                }
                echo 'All tests passed successfully!'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                script {
                    if (isUnix()) {
                        sh "docker build -t ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG} ."
                        sh "docker build -t ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest ."
                    } else {
                        bat "docker build -t ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG} ."
                        bat "docker build -t ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest ."
                    }
                }
                echo 'Docker image built successfully!'
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                script {
                    if (isUnix()) {
                        sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                        sh "docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
                        sh "docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"
                        sh "docker logout"
                    } else {
                        bat "docker login -u ${DOCKERHUB_CREDENTIALS_USR} -p ${DOCKERHUB_CREDENTIALS_PSW}"
                        bat "docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
                        bat "docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"
                        bat "docker logout"
                    }
                }
                echo 'Docker image pushed successfully!'
            }
        }
        
        stage('Verify Docker Image') {
            steps {
                echo 'Verifying Docker image...'
                script {
                    if (isUnix()) {
                        sh 'docker images | grep todo-app'
                    } else {
                        bat 'docker images | findstr todo-app'
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo '✓ Pipeline completed successfully!'
            echo "Docker image: ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
        }
        failure {
            echo '✗ Pipeline failed!'
        }
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
    }
}
