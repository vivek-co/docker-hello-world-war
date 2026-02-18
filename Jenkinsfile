pipeline {
    agent {
        docker {
        image 'docker:latest'
        args '-v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker'
    }
}

    environment {
        IMAGE_NAME   = "sahanasp123/hello-world-war"
        IMAGE_TAG    = "${BUILD_NUMBER}"
        DOCKER_CREDS = "dockerhub-creds"
        CONTAINER_NAME = "hello-war-container"
        HOME = "${WORKSPACE}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/vivek-co/docker-hello-world-war.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: DOCKER_CREDS,
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Deploy to Tomcat Server') {
            steps {
                sh """
                docker pull ${IMAGE_NAME}:${IMAGE_TAG}
                docker rm -f ${CONTAINER_NAME} || true
                docker run -d -p 8085:8080 --name ${CONTAINER_NAME} ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }
}
