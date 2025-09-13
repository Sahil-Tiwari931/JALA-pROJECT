pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        AWS_ACCOUNT_ID = "183071452284"
        IMAGE_NAME = "jala"
        IMAGE_TAG = "latest"
        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${IMAGE_NAME}"
    }

    stages {
        stage('Login to AWS ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ECR_URI
                '''
            }
        }

        stage('Clone GitHub Repo') {
            steps {
                sh 'git clone https://github.com/Sahil-Tiwari931/JALA-pROJECT.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG DockerProject/'
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                docker tag $IMAGE_NAME:$IMAGE_TAG $ECR_URI:$IMAGE_TAG
                docker push $ECR_URI:$IMAGE_TAG
                '''
            }
        }

        stage('Run Docker Image') {
            steps {
                sh 'docker run -d -p 8090:80 $ECR_URI:$IMAGE_TAG'
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
            mail to: 'Vershitiwari0@gmail.com',
                 subject: 'Jenkins Job Success',
                 body: 'Your Docker image was successfully built and deployed to AWS ECR.'
        }
        failure {
            echo '❌ Deployment failed!'
            mail to: 'Vershitiwari0@gmail.com',
                 subject: 'Jenkins Job Failed',
                 body: 'Something went wrong during the pipeline execution. Please check the logs.'
        }
    }
}



