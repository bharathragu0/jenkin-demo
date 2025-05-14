pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = credentials('BETA_AWS_ACCOUNT_ID')        // Store in Jenkins credentials
        AWS_DEFAULT_REGION = 'us-west-2'                            // Or your region
        IMAGE_REPO_NAME = "practice-jenkins-image"
        IMAGE_TAG = "v1"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }

    stages {
        stage('Clone GitHub Repo') {
            steps {
                git branch: 'main',
                    credentialsId: 'GITHUB_TOKEN',                  // Set in Jenkins credentials
                    url: 'https://github.com/bharathragu0/new-jenkin.git'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY',
                    credentialsId: 'BETA_AWS_CRED'
                ]]) {
                    sh """
                        aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${REPOSITORY_URI}
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_REPO_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                script {
                    docker.withRegistry("https://${REPOSITORY_URI}", 'BETA_AWS_CRED') {
                        dockerImage.push("${IMAGE_TAG}")
                    }
                }
            }
        }
    }
}

