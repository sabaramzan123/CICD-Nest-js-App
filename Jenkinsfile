pipeline {
    agent any

    environment {
        CONTAINER_NAME = "nestjs-app"
        IMAGE_NAME     = "nest_js_cicd"
        PORT           = "3000"
        EMAIL          = "sabaramzan0226@gmail.com"
        TAR_PATH       = "/home/ubuntu/nestjs-app.tar"
    }

    stages {

        stage("Clone Repo") {
            steps {
                git branch: 'main', url: 'https://github.com/sabaramzan123/CICD-Nest-js-App'
            }
        }

        stage("Stop & Remove Old Container") {
            steps {
                sh '''
                echo "Stopping old container (if running)..."
                sudo docker stop $CONTAINER_NAME || true
                sudo docker rm $CONTAINER_NAME || true
                '''
            }
        }

        stage("Load Docker Image") {
            steps {
                sh '''
                echo "Loading image from tar..."
                sudo docker load -i ${TAR_PATH}
                '''
            }
        }

        stage("Run Container") {
            steps {
                sh '''
                echo "Starting new container..."
                sudo docker run -d -p ${PORT}:${PORT} --name $CONTAINER_NAME $IMAGE_NAME:latest
                '''
            }
        }

        stage("Send Email Notification") {
            steps {
                emailext(
                    subject: "NestJS App Deployed Successfully",
                    body: "Your NestJS application is live: http://${env.NODE_NAME}:${PORT}/",
                    to: "${EMAIL}"
                )
            }
        }
    }
}
