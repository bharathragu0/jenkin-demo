pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
               git branch: 'main', url: 'https://github.com/bharathragu0/jenkin-demo.git'
            }
        }

        stage('Deploy') {
            steps {
                sh 'chmod +x deploy.sh'
                sh './deploy.sh'
            }
        }
    }
}

