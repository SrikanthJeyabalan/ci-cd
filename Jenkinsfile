pipeline {
    agent any

    environment {
        AWS_REGION     = 'ap-south-1'
        AWS_ACCOUNT_ID = '058264461329'
        ECR_REPOSITORY = 'task-1'
        IMAGE_NAME     = 'my-nginx-image'
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}"
    }

    stages {
        stage('Build Image') {
            steps {
                sh 'docker build -t "$IMAGE_NAME:latest" .'
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([
                    string(credentialsId: 'AWS_ACCESS_KEY_ID',
                           variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'AWS_SECRET_ACCESS_KEY',
                           variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh '''
                        set -e
                        aws ecr get-login-password --region "$AWS_REGION" |
                        docker login --username AWS --password-stdin \
                          "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
                    '''
                }
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                    set -e
                    docker tag "$IMAGE_NAME:latest" "$REPOSITORY_URI:latest"
                    docker push "$REPOSITORY_URI:latest"
                '''
            }
        }
    }

    post {
        success {
            echo 'Image built and pushed to ECR successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the console output.'
        }
        always {
            sh 'docker logout "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com" || true'
        }
    }
}
