pipeline {
  agent any
  stages {
    stage ('SCM checkout and docker login') {
      steps {
        checkout scm
        sh 'aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 489994096722.dkr.ecr.us-west-2.amazonaws.com'
      }
    }
    stage ('Test') {
      steps {
        sh 'npm install --only=dev'
        sh 'npm test'
      }      
    }
    stage ('Docker build/Push') {
      steps {
        sh 'docker build -t nodeapp .'
        sh 'docker tag nodeapp:latest 489994096722.dkr.ecr.us-west-2.amazonaws.com/nodeapp:latest'
        sh 'docker push 489994096722.dkr.ecr.us-west-2.amazonaws.com/nodeapp:latest'
      }
    }
    stage ('Deploy app on EC2') {
      steps {
        sshagent (credentials: ['sherryinstance']) {
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.245.202.139 uptime'
          sh 'ssh -v ubuntu@54.245.202.139 whoami'
          sh 'ssh -v ubuntu@54.245.202.139 aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 489994096722.dkr.ecr.us-west-2.amazonaws.com'
          sh 'ssh -v ubuntu@54.245.202.139 docker pull 489994096722.dkr.ecr.us-west-2.amazonaws.com/nodeapp:latest'
          sh 'ssh -v ubuntu@54.245.202.139 docker run -d -p 3000 489994096722.dkr.ecr.us-west-2.amazonaws.com/nodeapp:latest'
        }
      }
    }
   }
  post {
    success {
      slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '$JOB_NAME [$BUILD_NUMBER]' ($BUILD_URL)")
    }
    failure {
      slackSend (color: '#FF0000', message: "FAILED: Job '$JOB_NAME [$BUILD_NUMBER]' ($BUILD_URL)")
    }
  }      
 }
                                    
