pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "nodejs-app"
        CONTAINER_NAME = "nodejs-container"
        PORT = "3000"
        REPO_URL = "https://github.com/AishaMohamed2022/task3_multibranch.git"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Cloning project from GitHub...'
                checkout scm
            }
        }

      stages {
        stage('Getting Repo files') {
            steps {
                git branch: "${GIT_BRANCH}", credentialsId: 'jenkins', url: "${REPO_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t ${DOCKER_IMAGE}:build-${BUILD_NUMBER} .'
            }
        }

        stage('Run Container') {
            steps {
                echo 'Stopping old container if it exists...'
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true
                '''

                echo 'Starting new container...'
                sh '''
                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    -p ${PORT}:3000 \
                    ${DOCKER_IMAGE}:build-${BUILD_NUMBER}
                '''
            }
        }

        stage('Health Check') {
            steps {
                echo 'Checking application health...'
                sh '''
                    sleep 5
                    curl -f http://localhost:${PORT}/health
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }

        always {
            echo 'Cleaning unused Docker images...'
            sh '''
                docker image prune -f || true
            '''
        }
    }
}

