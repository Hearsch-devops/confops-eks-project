pipeline {

    agent { label 'eks' }

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO = 'confops'
        IMAGE_TAG = "${BUILD_NUMBER}"
        AWS_ACCOUNT_ID = '< _ACCOUNT_ID_ >'
        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                credentialsId: 'gitlab-creds',
                url: 'https://gitlab.com/devautoflow/conference-room-setup.git'
            }
        }

        stage('Verify Tools') {
            steps {
                sh '''
                    docker --version
                    aws --version
                    kubectl version --client
                    sonar-scanner --version
                '''
            }
        }

        stage('SonarCloud Scan') {
            steps {
                withCredentials([string(credentialsId: 'sonarqube_token', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        sonar-scanner \
                        -Dsonar.projectKey=Hearsch-devops_ConfOps \
                        -Dsonar.organization=hearsch-devops \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=https://sonarcloud.io \
                        -Dsonar.token=$sonarqube_token \
                        -Dsonar.javascript.node.maxspace=1024
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t confops:${IMAGE_TAG} ./Conference-setup
                '''
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                sh '''
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS --password-stdin $ECR_URI
                '''
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                    docker tag confops:${IMAGE_TAG} $ECR_URI:${IMAGE_TAG}
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                    docker push $ECR_URI:${IMAGE_TAG}
                '''
            }
        }


        stage('Create Namespace') {
            steps {
                sh '''
                    kubectl create namespace conference-room --dry-run=client -o yaml | kubectl apply -f -
                '''
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''

                    kubectl apply -f Kubernetes-setup/
                    kubectl set image deployment/flask-deployment \
                    flask=$ECR_URI:$IMAGE_TAG \
                    -n conference-room
                    kubectl rollout status deployment/flask-deployment \
                    -n conference-room
                '''
            }
        }
    }

    post {

        success {
            echo 'Deployment Successful!'
        }

        failure {
            echo 'Pipeline Failed!'
        }
    }
}
