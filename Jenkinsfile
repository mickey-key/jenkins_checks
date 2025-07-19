pipeline {
  agent any
  stages {
    stage('Clone repository') {       
      steps {
             checkout scm    
      }
    }    
      
     stage('Build image') { 
       steps { 
         script{
           app = docker.build("mickeykey/hello-flask-app:${env.BUILD_ID}","./flask_app")    
         }
       }
     }     
 
    stage('Push image') {
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
          app.push("${env.BUILD_NUMBER}")            
          app.push("latest")        
          }
        }
      }
    }  
  }
 
  post {
    success {
      script {
        emailext(
          subject: "Jenkins pipeline '${env.JOB_NAME}' completed successfully",
            body: "Pipeline '${env.JOB_NAME}' (#${env.BUILD_NUMBER}) completed successfully.",
          to: 'happymomentsmickey@gmail.com' 
        )
      }
    }
    failure {
      script {
        emailext(
          subject: "Jenkins pipeline '${env.JOB_NAME}' failed",
            body: "Pipeline '${env.JOB_NAME}' (#${env.BUILD_NUMBER}) failed. Log: ${env.BUILD_URL}",
          to: 'happymomentsmickey@gmail.com' 
        )
      }
    }
  }
}
 
