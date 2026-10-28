pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        APP_NAME = 'keshavagrawal2006/flask-devops-app'
        MINIKUBE_HOME = '/home/ubuntu'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub...'
                checkout scm
            }
        }

        stage('Build and Test') {
            steps {
                echo 'Building and running automated tests...'
                sh '''
                    python3 -m py_compile app.py test.py
                    python3 test.py
                '''
            }
        }

        stage('Docker Build') {
            steps {
                echo "Building Docker images with tag ${BUILD_NUMBER} and latest..."
                sh '''
                    docker build -t ${APP_NAME}:${BUILD_NUMBER} -t ${APP_NAME}:latest .
                '''
            }
        }

        stage('Push Image') {
            steps {
                echo 'Pushing Docker images to Docker Hub...'
                sh '''
                    echo "$DOCKERHUB_CREDENTIALS_PSW" | docker login -u "$DOCKERHUB_CREDENTIALS_USR" --password-stdin
                    docker push ${APP_NAME}:${BUILD_NUMBER}
                    docker push ${APP_NAME}:latest
                '''
            }
        }

        stage('Minikube Deployment') {
            steps {
                echo "Deploying application to Minikube using image tag ${BUILD_NUMBER}..."
                sh '''
                    sed -i "s|image: .*|image: ${APP_NAME}:${BUILD_NUMBER}|" k8s/deployment.yaml
                    kubectl apply -f k8s/
                    kubectl rollout status deployment/flask-hello-deployment --timeout=120s
                '''
            }
        }

        stage('Verification') {
            steps {
                echo 'Verifying running pods, deployment, service, and application endpoint...'
                sh '''
                    kubectl get deployments -o wide
                    kubectl get pods -o wide
                    kubectl get svc -o wide
                    MINIKUBE_IP=$(minikube ip 2>/dev/null || echo "192.168.49.2")
                    echo "Application NodePort endpoint: http://${MINIKUBE_IP}:30008"
                    curl -fsS "http://${MINIKUBE_IP}:30008" || curl -fsS "http://localhost:30008"
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}
