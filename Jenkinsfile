
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                credentialsId: 'github-creds',
                url: 'https://github.com/YOUR_USERNAME/cicd-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t cicd-app:${BUILD_NUMBER} .'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker rm -f cicd-container || true
                docker run -d -p 3000:3000 --name cicd-container cicd-app:${BUILD_NUMBER}
                '''
            }
        }
    }
}
