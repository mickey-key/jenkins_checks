node {    
      def app     
     stage('Clone repository') {               
                  checkout scm    
      }    
    stage ('test') {
            script {
            echo "Running tests..."
            sh '''
            docker --help
            '''
        }
    }
    
    stage('Build image') { 
         script {
            sh '''
            cd flask_app
            '''
         }
    app = docker.build("hello-flask-app:${env.BUILD_ID}","./flask_app")    
       }           
    stage('Test image') {                       
        app.inside {            
             sh 'echo "Tests passed"'        
            }    
        }            
      stage('Push image') {
     docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
         app.push("${env.BUILD_NUMBER}")            
         app.push("latest")        
              }    
           }
       stage('delpoy to kubernetes') { 
         steps {
             script {
              sh '''
              helm install my-hello-app ./helm 
              '''
              }
       steps {
            container('helm') {
              script {
                echo "Deploying to Kubernetes with Helm..."
                  sh '''
                    helm upgrade -f ./helm/values.yaml \\
                    --namespace default                  '''
                }
              } 
         }}
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
