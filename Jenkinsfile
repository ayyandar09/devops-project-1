pipeline {
    agent any

    environment {
       DOCKER_CREDENTIALS = credentials('docker_user')
    }

    stages {
        stage('Checkout Code') {
        steps {
            git branch: 'main', url: 'https://github.com/ayyandar09/devops-project-1.git'
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

        //For Kubeconfig stored as secret file
        // stage('Deploy to Minikube') {
        //     steps {
        //         withCredentials([file(credentialsId: 'kubeconfig', variable: 'KCFG')]) {
        //             sh '''
        //             export KUBECONFIG=$KCFG
        //             kubectl apply -f k8s/deployment.yaml
        //             kubectl apply -f k8s/service.yaml
        //             '''
        //         }
        //     }
        // }

        //This is for kubeconfig stored as secret text
        stage('Deploy to Minikube') {
            steps {
              withCredentials([string(credentialsId: 'kubeconfig', variable: 'KCFG_CONTENT')]) {
                  sh '''
                  # Write kubeconfig text into a temporary file
                  echo "$KCFG_CONTENT" > kubeconfig.yaml

                  # Point kubectl to it
                  export KUBECONFIG=$(pwd)/kubeconfig.yaml

                  # Deploy to Minikube
                  kubectl apply -f k8s/deployment.yaml
                  kubectl apply -f k8s/service.yaml
                  '''
                }
            }
        }

    }
}
