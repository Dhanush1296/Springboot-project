pipeline {
    agent any

    environment {
        IMAGE_NAME = 'dhanush46656/spring-petclinic'
        K8S_NAMESPACE = 'default'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Dhanush1296/Springboot-project.git'
            }
        }
        
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
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

        stage('Print Workspace') {
            steps {
                echo "Workspace path is: ${env.WORKSPACE}"
                    sh 'ls -l $WORKSPACE'
                }
            }

        stage('Deploy to Minikube') {
            steps {
                dir("${env.WORKSPACE}"){
                    sh '''
                    kubectl config use-context minikube
                    kubectl apply -f k8s/db.yml --validate=false
                    kubectl apply -f k8s/petclinic.yml --validate=false
                    '''
                }
            }
        }
    }
}

