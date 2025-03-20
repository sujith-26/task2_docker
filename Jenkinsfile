pipeline {
    agent any

    environment {
        IMAGE_NAME = "sujith2606/demo"  // Use a proper Docker Hub repository name
        TAG = "latest"
        CONTAINER_NAME = "my-container"
        PORT = "3001"
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "Cloning GitHub repository..."
                git branch: 'main', url: 'https://github.com/sujith-26/task2_docker.git'
            }
        }

        stage('Verify Files') {
            steps {
                echo "Checking if required files exist..."
                sh 'ls -l'
                sh '[ -f build.sh ] && echo "build.sh found" || { echo "Error: build.sh not found!"; exit 1; }'
                sh '[ -f deploy.sh ] && echo "deploy.sh found" || { echo "Error: deploy.sh not found!"; exit 1; }'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh 'chmod +x build.sh'
                sh 'set -e; ./build.sh'  // Ensure script exits on failure
            }
        }

        stage('Login to Docker Hub') {
            steps {
                echo "Logging into Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Tagging and pushing Docker image..."
                sh "docker tag $IMAGE_NAME:$TAG $DOCKER_USER/$IMAGE_NAME:$TAG"
                sh "docker push $DOCKER_USER/$IMAGE_NAME:$TAG"
            }
        }

        stage('Deploy Docker Container') {
            steps {
                echo "Deploying Docker container..."
                sh 'chmod +x deploy.sh'
                sh 'set -e; ./deploy.sh'  // Ensure script exits on failure
            }
        }
    }

    post {
        success {
            echo "Deployment Successful!"
        }
        failure {
            echo "Deployment Failed!"
        }
    }
}
