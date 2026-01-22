pipeline {
    agent any

    environment {
        IMAGE = "vignesh/cicd-app:${BUILD_NUMBER}"
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

        stage('Stop Old Container (Dev)') {
            steps {
                sh "docker rm -f cicd-container || true"
            }
        }

        stage('Run New Container (Dev)') {
            steps {
                sh "docker run -d -p 3000:3000 --name cicd-container $IMAGE"
            }
        }

        stage('Push Docker Image (Optional)') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub', url: '']) {
                    sh "docker tag $IMAGE yourdockerhubusername/cicd-app:$BUILD_NUMBER"
                    sh "docker push yourdockerhubusername/cicd-app:$BUILD_NUMBER"
                }
            }
        }

        stage('Deploy to Dev (K8s)') {
            steps {
                sh "microk8s kubectl apply -f k8s/dev.yaml"
            }
        }

        stage('Approval for Prod') {
            steps {
                input message: "Deploy to PROD?"
            }
        }

        stage('Deploy to Prod (Blue-Green)') {
            steps {
                // Deploy both blue and green
                sh "microk8s kubectl apply -f k8s/prod.yaml"
                // Switch service selector for blue-green
                sh "microk8s kubectl patch service app-prod-service -p '{\"spec\":{\"selector\":{\"app\":\"cicd\",\"version\":\"blue\"}}}'"
            }
        }
    }

    post {
        success {
            slackSend message: "Build ${BUILD_NUMBER} Success 🚀"
        }
        failure {
            slackSend message: "Build ${BUILD_NUMBER} Failed ❌"
        }
    }
}
