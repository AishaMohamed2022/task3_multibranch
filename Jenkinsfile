pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "nodejs-app"
        CONTAINER_NAME = "nodejs-container"
        PORT = "3000"
        REPO_URL = "https://github.com/AishaMohamed2022/task3_multibranch.git"
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

                sh '''
                    docker build -t ${DOCKER_IMAGE}:build-${BUILD_NUMBER} .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {

                        sh """
                            echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin

                            docker tag ${APP_NAME}:${BUILD_NUMBER} ${DOCKER_USERNAME}/${APP_NAME}:${BUILD_NUMBER}

                            docker push ${DOCKER_USERNAME}/${APP_NAME}:${BUILD_NUMBER}
                        """

                    }
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

