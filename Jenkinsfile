pipeline {
    agent any

    environment {
        // Customize these for your project
        REGISTRY = "docker.io"
        IMAGE_NAME = "your-dockerhub-username/your-frontend-app"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "📥 Checking out code..."
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "🏗️ Building project..."
                // For HTML/CSS/JS, this might include npm or build tools
                sh '''
                    if [ -f package.json ]; then
                        npm install
                        npm run build || echo "No build script found, skipping..."
                    else
                        echo "No package.json found, skipping npm build."
                    fi
                '''
            }
        }

        stage('Test') {
            steps {
                echo "🧪 Running tests..."
                sh '''
                    if [ -f package.json ]; then
                        npm test || echo "No test script found, skipping tests..."
                    else
                        echo "No tests to run."
                    fi
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Building Docker image..."
                sh '''
                    docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "🚀 Pushing Docker image to registry..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin $REGISTRY
                        docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Build and deployment successful!"
        }
        failure {
            echo "❌ Build or deployment failed."
        }
    }
}
