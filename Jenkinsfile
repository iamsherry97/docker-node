pipeline {
  agent {
    def notifyStarted() {
  // send to Slack
  slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
  }
    node {
      notifyStarted()
  }
}
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
 }
// node {
//    def commit_id
//    stage('Preparation') {
//      checkout scm
//      sh "git rev-parse --short HEAD > .git/commit-id"
//      commit_id = readFile('.git/commit-id').trim()
//    }
//    stage('test') {
//      def myTestContainer = docker.image('node:4.6')
//      myTestContainer.pull()
//      myTestContainer.inside {
//        sh 'npm install --only=dev'
//        sh 'npm test'
//      }
//    }
                                        
//    stage('docker build/push') {            
//      docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
//        def app = docker.build("wardviaene/docker-nodejs-demo:${commit_id}", '.').push()
//      }                                     
//    }                                       
// }                                          
