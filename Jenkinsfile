pipeline {
agent any
  environment {
     DOCKERHUB_CREDENTIALS=credentials('thangdocker')
  }
  stages {
    stage('Login') {
      steps {
          sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
        }
	  }
    stage("push image to dockerhub"){
      steps{
        dir ("jenkins-k8s-demo/httpd"){
          // Run Maven on a Unix agent.
          sh "docker build . -t thangsu/weather-web:1.0.0"
          sh 'docker push thangsu/weather-web:1.0.0'
          }
        }
     }
     stage('Deploy to k8s'){
        steps{
          sshagent(['k8s-master']) {
            sh "scp -o StrictHostKeyChecking ./k8s root@192.168.0.145:/"
            script{
              try{
                sh "ssh root@192.168.0.145 kubectl apply -f ./k8s"
              }catch(error){
                sh "ssh root@192.168.0.145 kubectl create -f ./k8s"
              }
            }
            
          }
        }
     }
  }
    
}

