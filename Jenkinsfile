pipeline {
    agent any

    environment {
       DOCKER_CREDENTIALS = credentials('dockerhub')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/ayyandar09/devops-project-1.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ayyandar/devops-app:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                    echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin
                    docker push ayyandar/devops-app:latest
                '''
            }
        }

        stage('Deploy to Minikube') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KCFG')]) {
                    sh '''
                    export KUBECONFIG=$KCFG
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                    '''
                }
            }
        }
    }
}
