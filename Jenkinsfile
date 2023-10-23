pipeline {
  agent any
    
  tools {nodejs "node"}
    
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


        stage('Deploy Docker Image to DockerHub') {
            steps {
                script {
                 withCredentials([string(credentialsId: 'nlakshminath', variable: 'nlakshminath')]) {
                    sh 'docker login -u nlakshminath -p ${nlakshminath}'
                 }  
                 sh 'docker push nlakshminath/node-app'
                }
            }
        }
         
     stage('Deploying Node App helm chart on eks') {
      steps {
        script {
             sh ('aws eks --region us-east-1 update-kubeconfig --name iff-dev-eks-cluster')
             sh ('kubectl get  ns')
             sh ('helm install nodeapp ./node-app')
         }
      }
    }

  }
}
