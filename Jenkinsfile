pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/nive1222/Jenkinsandjava.git'
        AWS_REGION = 'ap-south-1'
        ECR_REPO_NAME = 'paswan2527'
        ECR_PUBLIC_REPO_URI = 'public.ecr.aws/d4e4e2u2/paswan2527'
        IMAGE_TAG = 'latest'
        AWS_ACCOUNT_ID = '843922065696'
        IMAGE_URI = "${ECR_PUBLIC_REPO_URI}:${IMAGE_TAG}"
    }

    stages {
             stage('Configure AWS Credentials') {
    steps {
        script {
            sh '''
            echo "Setting up AWS credentials for Jenkins..."
            mkdir -p /var/lib/jenkins/.aws
            echo "[default]" > /var/lib/jenkins/.aws/credentials
            echo "aws_access_key_id=AKIA4I7NMZEQP5IBG5F6" >> /var/lib/jenkins/.aws/credentials
            echo "aws_secret_access_key=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" >> /var/lib/jenkins/.aws/credentials
            chown -R jenkins:jenkins /var/lib/jenkins/.aws
            '''
        }
    }
}


        stage('Clone Repository') {
            steps {
                checkout([
    $class: 'GitSCM',
    branches: [[name: 'main']],
    userRemoteConfigs: [[
        url: "${GIT_REPO}",
        credentialsId: 'github-creds'
    ]]
])
            }
        }

        stage('Build') {
            steps {
                script {
                    sh '''
                        echo "Building Java application..."
                        mvn clean -B -Denforcer.skip=true package
                    '''
                }
            }
        }

        stage('Login to AWS ECR') {
            steps {
                script {
                    sh '''
                        echo "Logging into AWS ECR..."
                        aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh '''
                        echo "Building Docker image..."
                        docker build -t ${IMAGE_URI} .
                    '''
                }
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                script {
                    sh '''
                        echo "Pushing Docker image to ECR..."
                        docker push ${IMAGE_URI}
                    '''
                }
            }
        }
    }
    
    post {
        success {
            echo "Docker image pushed to ECR successfully and deployed."
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
