pipeline {
    agent any

    environment {
        IMAGE_NAME = 'devops-demo'
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/SriJainthu/devops-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    COMMIT_HASH = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    IMAGE_TAG = "${IMAGE_NAME}:${COMMIT_HASH}"

                    echo "Building Docker image: ${IMAGE_TAG}"
                    sh "docker build -t ${IMAGE_TAG} ."
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh "docker run --rm ${IMAGE_TAG}"
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        echo "Logging into DockerHub as $DOCKER_USERNAME"
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                        sh "docker tag ${IMAGE_TAG} ${DOCKER_USERNAME}/${IMAGE_TAG}"
                        sh "docker push ${DOCKER_USERNAME}/${IMAGE_TAG}"
                    }
                }
            }
        }

    }
}