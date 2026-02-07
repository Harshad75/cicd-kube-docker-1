pipeline {
  agent { 
    node { 
      label 'agentrenew'
    }
  }
  parameters {
    string(name: 'IMAGE_NAME', defaultValue: 'index-page', description: 'Docker image name')
    string(name: 'IMAGE_TAG',  defaultValue: '1.0', description: 'Docker image tag')
    string(name: 'AWS_REGION_PARAM', defaultValue: 'us-east-1', description: 'AWS REGION')
    string(name: 'AWS_LIFE_CYCLE_ID', description: 'AWS Account ID')
  }

  environment {
    AWS_REGION = '${AWS_REGION_PARAM}'
    ECR_REPO   = '${AWS_LIFECYCLE_ID}.dkr.ecr.us-east-1.amazonaws.com/html-app'
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
  }
}

