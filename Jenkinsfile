pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'sujith2606/demo:latest' // Correct image name used in build step
        DOCKER_TAG = 'sujith2606/task2_docker:latest' // Proper tag for pushing to Docker Hub
    }
    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning GitHub repository...'
                git 'https://github.com/sujith-26/task2_docker.git'
            }
        }
        stage('Verify Files') {
            steps {
                echo 'Checking if required files exist...'
                sh 'ls -l'
                sh '[ -f build.sh ] && echo "build.sh found" || exit 1'
                sh '[ -f deploy.sh ] && echo "deploy.sh found" || exit 1'
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'chmod +x build.sh && ./build.sh'
            }
        }
        stage('Login to Docker Hub') {
            steps {
                echo 'Logging into Docker Hub...'
                withCredentials([string(credentialsId: 'docker-hub-password', variable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u sujith2606 --password-stdin'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                withCredentials([string(credentialsId: 'docker-hub-password', variable: 'DOCKER_PASS')]) {
                    sh """
                        docker tag $DOCKER_IMAGE $DOCKER_TAG
                        docker push $DOCKER_TAG
                    """
                }
            }
        }
        stage('Deploy Docker Container') {
            steps {
                echo 'Deploying Docker container...'
                sh './deploy.sh'
            }
        }
    }
    post {
        failure {
            echo '❌ Deployment Failed! Check logs for errors.'
        }
        success {
            echo '✅ Deployment Successful!'
        }
    }
}
