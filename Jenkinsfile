pipeline {
    agent any
    // triggers{
    //     pollSCM('* * * * *')
    // }
    environment {
        AWS_ACCOUNT_ID = '183071452284'
        AWS_REGION = 'eu-north-1'
        ECR_REPO_NAME = 'jala-project-1'
        IMAGE_TAG = 'latest'  // You can dynamically set the build version
        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}:${IMAGE_TAG}"
        EMAIL = "Vershitiwari0@gmail.com"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $ECR_REPO_NAME .
                '''
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                docker tag $ECR_REPO_NAME:$IMAGE_TAG $ECR_URI
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                docker push $ECR_URI
                '''
            }
        }

      stage('Run Docker Container') {
        steps {
            sh '''
                # Remove all existing Docker images
                docker rmi -f $(docker images -q) || true
                
                # Pull the latest image from ECR
                docker pull $ECR_URI
                
                # Stop and remove existing container named 'app'
                docker stop app || true
                docker rm app || true
                
                # Run the container
                docker run -d --name app -p 8090:80 $ECR_URI
            '''
        }
}

    }

    post {
        success {
            mail to: "${email}",
                 subject: "Job '${env.JOB_NAME}' #${env.BUILD_NUMBER} Succeeded",
                 body: "Good news! The Jenkins job succeeded."
        }
        failure {
            mail to: "${email}",
                 subject: "Job '${env.JOB_NAME}' #${env.BUILD_NUMBER} Failed",
                 body: "Unfortunately, the Jenkins job failed. Please check the logs."
        }
    }
}
