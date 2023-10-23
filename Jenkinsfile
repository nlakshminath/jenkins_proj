pipeline {
    agent any
    
    tools {nodejs "node"}
    
    environment {
        ECR_REPOSITORY_URI = "666008372373.dkr.ecr.us-east-1.amazonaws.com/node-app"
    }
    
    stages {
        stage("Clone code from GitHub") {
            steps {
                script {
                    checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/nlakshminath/jenkins_proj.git']]])
                }
            }
        }
        
        stage('Node JS Build') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Build Node JS Docker Image') {
            steps {
                script {
                    sh 'docker build -t nlakshminath/node-app .'
                }
            }
        }
        
        stage('Tag Docker Image for ECR') {
            steps {
                script {
                    sh "docker tag nlakshminath/node-app:latest ${ECR_REPOSITORY_URI}:latest"
                }
            }
        }
        
        stage('Deploy Docker Image to ECR') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'aws', variable: 'AWS_TOKEN')]) {
                        sh "echo $AWS_TOKEN | docker login -u AWS --password-stdin ${ECR_REPOSITORY_URI}"
                        sh "docker push ${ECR_REPOSITORY_URI}:latest"
                    }
                }
            }
        }
        
        stage('Deploying Node App helm chart on eks') {
            steps {
                script {
                    sh 'aws eks --region us-east-1 update-kubeconfig --name iff-dev-eks-cluster'
                    sh 'kubectl get ns'
                    sh 'helm install nodeapp ./node-app'
                }
            }
        }
    }
}
