pipeline {
    agent any

    environment {
        CONTAINER_NAME = "nestjs-app"
        IMAGE_NAME = "nestjs-image"
        PORT = "3000"
        EMAIL = "s21486770@gmail.com"
    }

    stages {
        stage("Clone Repo") {
            steps {
                git branch: 'main', url: 'https://github.com/sabaramzan123/Nestjs-CICD-with-Docker-and-Jenkins'
            }
        }

        stage("Stop and Remove Old Container") {
            steps {
                sh '''
                   sudo docker stop $CONTAINER_NAME || true
                   sudo docker rm $CONTAINER_NAME || true
                '''
            }
        }

        stage("Load Docker Image") {
            steps {
                sh '''
                   sudo docker load -i /home/ubuntu/nestjs-app.tar
                '''
            }
        }

        stage("Run Container") {
            steps {
                sh '''
                   sudo docker run -d -p ${PORT}:${PORT} --name $CONTAINER_NAME $IMAGE_NAME:latest
                '''
            }
        }

        stage("Send Email Notification") {
            steps {
                emailext(
                    subject: "NestJS App Deployed Successfully 🚀",
                    body: "Your app is live at: http://${env.NODE_NAME}:${PORT}/",
                    to: "${EMAIL}"
                )
            }
        }
    }
}
