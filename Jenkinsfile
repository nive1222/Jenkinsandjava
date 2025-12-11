pipeline {
    agent any

    environment {
        GIT_REPO       = 'https://github.com/nive1222/Jenkinsandjava.git'
        AWS_REGION     = 'us-east-1'
        ECR_REPO_NAME  = 'paswan2527'
        AWS_ACCOUNT_ID = '843922065696'
        IMAGE_TAG      = 'latest'
        IMAGE_URI      = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}:${IMAGE_TAG}"
    }

    stages {

        stage('Configure AWS Credentials') {
            steps {
                withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                    sh 'echo "AWS Credentials loaded."'
                }
            }
        }

        stage('Clone Repository') {
            steps {
                git url: "${GIT_REPO}", branch: 'main'
            }
        }

        stage('Build') {
            steps {
                sh '''
                    echo "Building Java application..."
                    mvn clean package -DskipTests
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                    sh '''
                        echo "Logging in to ECR..."
                        aws ecr get-login-password --region ${AWS_REGION} | \
                        docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "Building Docker image..."
                    docker build -t ${IMAGE_URI} .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                    echo "Pushing Docker image..."
                    docker push ${IMAGE_URI}
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
