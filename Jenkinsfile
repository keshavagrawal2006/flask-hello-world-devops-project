pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        APP_NAME = 'keshavagrawal2006/flask-devops-app'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Syntax Check') {
            steps {
                echo 'Building and running syntax check...'
                sh 'python3 -m py_compile app.py'
            }
        }

        stage('Docker Build & Push') {
            steps {
                sh 'docker build -t $APP_NAME:latest .'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push $APP_NAME:latest'
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }
    }
}
