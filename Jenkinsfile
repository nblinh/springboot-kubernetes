pipeline {
    agent any

       environment {
        DOCKER_IMAGE = "linhnb/spring-boot-app"
        K8S_NAMESPACE = "springboot-demo"
        DOCKER_CREDENTIALS_ID = 'linhnb@gmail.com' // Define Docker Hub credentials ID
        KUBECONFIG_CREDENTIALS_ID = 'Khongcogi80' // Define Kubernetes config credentials ID
    }
    tools {
        maven '3.9.9'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scm
                }
            }
        }

        stage('Build and Test') {
            steps {
                script {
                    sh "java -version"
                    sh "mvn -version"
                    sh "mvn clean package"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t spring-boot-app ."
                    sh "docker tag spring-boot-app linhnb/spring-boot-app"
                }
            }
        }

        stage('Push to dockerhub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'Khongcogi80', variable: 'DOCKER_PASSWORD')]) {
                        sh """
                            echo \$DOCKER_PASSWORD | docker login -u linhnb --password-stdin
                        """
                        sh "docker push linhnb/spring-boot-app"
                    }
                }
            }
        }


        stage('Deploy to Kubernetes') {
            steps {
                script {

                     kubeconfig(credentialsId: 'kubeconfig') {

                        sh 'kubectl apply -f k8s/deployment.yaml -n ${env.K8S_NAMESPACE}'
                        sh 'kubectl apply -f k8s/service.yaml -n ${env.K8S_NAMESPACE}'
                    }


                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Build and deployment successful!'
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}