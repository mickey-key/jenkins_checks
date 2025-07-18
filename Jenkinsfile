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
    app = docker.build("mi/flask-app")    
       }           
    stage('Test image') {                       
        app.inside {            
             sh 'echo "Tests passed"'        
            }    
        }            
      stage('Push image') {
     docker.withRegistry('https://registry.hub.docker.com', 'git') {
         app.push("${env.BUILD_NUMBER}")            
         app.push("latest")        
              }    
           }
        }
