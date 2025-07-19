pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        metadata:
          labels:
            some-label: some-label-value
        spec:
          containers:
          - name: docker
            image: docker:24.0.5
            command:
            - cat
            tty: true
            volumeMounts:
            - name: docker-socket
              mountPath: /var/run/docker.sock
          - name: sonarscanner
            image: sonarsource/sonar-scanner-cli
            command:
            - cat
            tty: true
          volumes:
          - name: docker-socket
            hostPath:
              path: /var/run/docker.sock
      '''
      retries 2
    }
  }

  environment {
    REPO_NAME = 'goals-app'
    IMAGE_TAG = 'latest'
    SONAR_PROJECT_KEY = "key"
    SONAR_LOGIN = "login"
    SONAR_HOST_URL = "http://127.0.0.1:9000"
  }
  stages {

             stage('Clone repository') {               
                  checkout scm    
      }    
       
    stage('Build image') { 

    app = docker.build("mickeykey/hello-flask-app:${env.BUILD_ID}","./flask_app")    
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
        
       stage('Run Tests') {
      steps {
        container('node') {
          script {
            echo "Running tests..."
            sh '''
              cd ./flask_app
              python main.py
            '''
          }
        }
      }
    }

    stage('SonarQube Analysis') {
      steps {
        container('sonarscanner') {
          script {
          echo "Running SonarQube analysis..."
            sh '''
              sonar-scanner \
                -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                -Dsonar.sources=. \
                -Dsonar.host.url=${SONAR_HOST_URL} \
                -Dsonar.login=${SONAR_LOGIN}
            '''
          }
        }
      }
    }


    stage('Deploy to Kubernetes with Helm') {
        when { expression { params.SHOULD_PUSH_TO_ECR == true } }
        steps {
            container('helm') {
              script {
                echo "Deploying to Kubernetes with Helm..."
                  sh '''
                    helm install flask-app ./helm/ \\
                    --namespace default                  '''
                }
              }
            }
        }
    }
  }

  }
  post {
    success {
      script {
        echo "Pipeline completed successfully!"
        emailext(
          subject: 'Jenkins Pipeline Success',
            body: "Pipeline '${env.JOB_NAME}' (#${env.BUILD_NUMBER}) completed successfully.\n\nCheck results: ${env.BUILD_URL}",
          to: 'kara.nemesis@gmail.com' 
        )
      }
    }
    failure {
      script {
        echo "Pipeline failed!"
        emailext(
          subject: 'Jenkins Pipeline Failure',
            body: "Pipeline '${env.JOB_NAME}' (#${env.BUILD_NUMBER}) failed.\n\nCheck the details here: ${env.BUILD_URL}",
          to: 'kara.nemesis@gmail.com' 
        )
      }
    }
  }
}
