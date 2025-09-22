pipeline {
    agent any

    environment {
        IMAGE_NAME = 'dhanush46656/spring-petclinic'
        K8S_NAMESPACE = 'default'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/Dhanush1296/spring-petclinic.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f k8s/db-deployment.yaml
                    kubectl apply -f k8s/app-deployment.yaml
                '''
            }
        }
    }
}

