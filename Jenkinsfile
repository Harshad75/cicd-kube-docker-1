pipeline {
  agent { 
    node { 
      label 'agentrenew'
    }
  }
  parameters {
    string(name: 'IMAGE_NAME', defaultValue: 'index-page', description: 'Docker image name')
    string(name: 'IMAGE_TAG',  defaultValue: '1.0', description: 'Docker image tag')
  }

  environment {
    AWS_REGION = 'us-east-1'
    ECR_REPO   = '563893393229.dkr.ecr.us-east-1.amazonaws.com/html-app'
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
        sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} html-app/.'
      }
    }
    stage('Login to ECR') {
      steps {
        sh '''
          aws ecr get-login-password --region $AWS_REGION \
          | docker login --username AWS --password-stdin $ECR_REPO
        '''
      }
    }

    stage('Tag & Push to ECR') {
      steps {
        sh '''
          docker tag ${IMAGE_NAME}:${IMAGE_TAG} $ECR_REPO:${IMAGE_TAG}
          docker push $ECR_REPO:${IMAGE_TAG}
        '''
      }
    }
    stage('SonarCloud Analysis') {
      steps {
        withSonarQubeEnv('sonarcloud') {
	  script {
	    def scannerHome = tool 'sonar-scanner'
      	    sh """
              ${scannerHome}/bin/sonar-scanner \
                -Dsonar.projectKey=Harshad75_cicd-kube-docker-1 \
                -Dsonar.organization=harshad75 \
                -Dsonar.sources=. \
                -Dsonar.branch.name=master
            """
	  }
        }
      }
    }
  }
}

