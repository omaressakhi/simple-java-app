pipeline {
    agent any
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t java-app .'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'Username', passwordVariable: 'Password')]) {
                        sh 'echo $Password | docker login --username $Username --password-stdin'
                        sh 'docker tag java-app $Username/java-app:latest'
                        sh 'docker push $Username/java-app:latest'
                    }
                }
            }
        }
        stage('Deploy to EKS') {
            steps {
                script {
                    withAWS(credentials: 'aws-cli', region: 'us-east-1') {
                        sh 'aws eks update-kubeconfig --region us-east-1 --name my-eks-cluster'
                        sh 'kubectl apply -f ./k8s/deployment.yaml'
                    }
                }
            }
        }
    }
}

