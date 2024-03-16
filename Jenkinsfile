   pipeline {
    agent any
    
    stages {
        stage('code') {
            steps {
               git url: "https://github.com/lost009/Aaptatt-Atul.git" , branch: "main"
            }
        }
        stage('maven build') {
            steps {
               
                sh 'mvn clean package'
            }
        }
        stage('docker image build') {
            steps {
               
                sh 'docker build -t sparkjava .'
            }
        }
        stage('scan') {
            steps {
                
                sh 'trivy image sparkjava'
            }
        }
        stage('push to dockerhub') {
            steps {
            
                withCredentials([usernamePassword(credentialsId:"DOCKERHUB",passwordVariable:"pass",usernameVariable:"user")]){
                sh "docker login -u ${env.user} -p ${env.pass}"
                sh "docker tag sparkjava:latest ${env.user}/sparkjava:latest"
                sh "docker push ${env.user}/sparkjava:latest"
                }
            }
        }
        stage('Deploy') {
            steps {
                sh "docker compose up -d"
                
            }
        }
    }
        post {
         always {
            emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
                to: 'kasanaatul86@gmail.com',                           
            attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
          }
        }
}
  
