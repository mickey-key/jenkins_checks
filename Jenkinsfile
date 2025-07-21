pipeline {
  agent {
     kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: jenkins-agent
    version: v1
spec:
 
    
  - name: python
    image: python:3.13.5-slim
    command: ["cat"]
    tty: true
    
    
  - name: docker
    image: docker:28
    command: ["cat"]
    tty: true
    volumeMounts:
      - name: docker-sock
        mountPath: /var/run/docker.sock
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock
        type: Socket
"""
        }
  }
 
  environment {
    IMAGE_TAG = 'latest'
    SONAR_PROJECT_KEY = ""
    SONAR_LOGIN = ""
    SONAR_HOST_URL = ""
  }
  stages {
    stage('Prepare') {
      steps {
        container('node') {
          script {
            echo "Cloning repository..."
            sh '''
            git clone https://github.com/mickey-key/jenkins_checks.git app
          echo "Repo files:"
              ls -la
            '''
          }
        }
      }
    }


    stage('Test application') {
            steps {
                container('python') {
		        dir('flask_app') {
		        sh '''
		        echo "Running tests..."
		        pytest main.py
		        '''
	                }
                }
            }
        }

    
    stage('Deploy to Kubernetes with Helm') {
        steps {
            container('helm') {
              script {
                echo "Deploying to Kubernetes with Helm..."
                  sh '''
                  helm install my-app ./app/helm --namespace default
                  '''
                }
              }
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
  
    stage('delete image') { 
       steps { 
         script{
           sh '''
           docker rmi mickeykey/hello-flask-app:${env.BUILD_ID} 
           '''
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
 
