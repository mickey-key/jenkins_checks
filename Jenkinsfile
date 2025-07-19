node {    
      def app     
     stage('Clone repository') {               
                  checkout scm    
      }    
       
    stage('Build image') { 

       stage('delpoy to kubernetes') { 
                    script {
              sh '''
              sudo snap install helm --classic
              helm install my-hello-app ./helm 
              '''
              }
       }
 
   
       stage('delpoy to kubernetes') { 
                   
            container('helm') {
              script {
                echo "Deploying to Kubernetes with Helm..."
                  sh '''
                    helm upgrade -f ./helm/values.yaml \\
                    --namespace default                  '''
                }
              } 
         }
       
 
   post {
    success {
      script {
        echo "Pipeline completed successfully"
        emailext(
          subject: 'task 6 pipeline successful',
            body: "Pipeline '${env.JOB_NAME}' (#${env.BUILD_NUMBER}) completed successfully",
          to: 'kara.nemesis@gmail.com' 
        )
      }
    }
    failure {
      script {
        echo "Pipeline failed!"
        emailext(
          subject: 'Pipeline failed',
            body: "Pipeline '${env.JOB_NAME}' (#${env.BUILD_NUMBER}) failed.\n\nCheck the details here: ${env.BUILD_URL}",
          to: 'kara.nemesis@gmail.com' 
        )
      }
    }
  }     
        }
