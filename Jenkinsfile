pipeline {
  agent { 
    node { 
      label 'agentrenew'
    }
  }  
  stages {
    stage('Testing') {
      steps {
	sh '''
	  whoami
	  id
	  groups
	  ls -l /var/run/docker.sock
	'''
      }
    }	

    stage('Checkout') {
      steps {
        git url: 'https://github.com/Harshad75/cicd-kube-docker-1.git', branch: 'master'
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t index-page:1.0 html-app/.'
      }
    }
  }
}

