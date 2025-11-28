pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = '303713699681'                 // Replace with your AWS account ID
        AWS_REGION = 'us-east-1'                        // Replace with your region
        IMAGE_NAME = 'nest_js_cicd'
        CONTAINER_NAME = 'nestjs_app'
        PORT = '3000'                                   // Port your app listens on inside container
        EMAIL = 'sabaramzan0226@gmail.com'             // Notification email
        EC2_IP = '107.23.162.42'                     // Replace with your EC2 public IP
        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${IMAGE_NAME}"
        
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/sabaramzan123/CICD-Nest-js-App'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME}:${env.BUILD_NUMBER} .
                """
            }
        }

        stage('Login to ECR') {
            steps {
                sh """
                aws ecr get-login-password --region ${AWS_REGION} | \
                docker login --username AWS --password-stdin ${ECR_URI}
                """
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                sh """
                docker tag ${IMAGE_NAME}:${env.BUILD_NUMBER} ${ECR_URI}:${env.BUILD_NUMBER}
                docker tag ${IMAGE_NAME}:${env.BUILD_NUMBER} ${ECR_URI}:latest
                docker push ${ECR_URI}:${env.BUILD_NUMBER}
                docker push ${ECR_URI}:latest
                """
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh """
                ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} '
                    aws ecr get-login-password --region ${AWS_REGION} | \
                    docker login --username AWS --password-stdin ${ECR_URI} &&
                    docker stop ${CONTAINER_NAME} || true &&
                    docker rm ${CONTAINER_NAME} || true &&
                    docker pull ${ECR_URI}:latest &&
                    docker run -d -p 80:${PORT} --name ${CONTAINER_NAME} --restart unless-stopped ${ECR_URI}:latest
                '
                """
            }
        }
    }

    post {
        success {
            emailext(
                subject: "NestJS App Deployed Successfully",
                body: "Your app is live at: http://${EC2_IP}/",
                to: "${EMAIL}"
            )
        }
        failure {
            emailext(
                subject: "NestJS App Deployment Failed",
                body: "Check Jenkins logs for details.",
                to: "${EMAIL}"
            )
        }
    }
}
