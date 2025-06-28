pipeline {
    agent any

    environment {
        IMAGE_NAME = 'your-app-name'
        IMAGE_TAG = 1.0
        BRANCH = "${env.BRANCH_NAME}"
        DOCKER_REGISTRY = 'hub.docker.com'
    }

    stages {
        stage('Build') {
            steps {
                echo '🔧 Building the application...'
                npm install
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running tests...'
                npm test
            }
        }

        stage('Docker Build') {
            steps {
                echo '🐳 Building Docker image...'
                sh """
                    docker build -t node$BRANCH:$IMAGE_TAG .
                """
            }
        }

        stage('Deploy') {
            steps {
                echo '🚀 Deploying application...'
                sh """
                    docker push $DOCKER_REGISTRY/node$BRANCH:$IMAGE_TAG
                """
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed.'
        }
    }
}