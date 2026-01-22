pipeline {
    agent any

    environment {
        IMAGE = "cicd-app:${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                credentialsId: 'github-creds',
                url: 'https://github.com/anishvignesh/cicd-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE ."
            }
        }

        stage('Stop Old Container') {
            steps {
                sh "docker rm -f cicd-container || true"
            }
        }

        stage('Run New Container') {
            steps {
                sh "docker run -d -p 3000:3000 --name cicd-container $IMAGE"
            }
        }
    }
}
