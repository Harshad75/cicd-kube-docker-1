pipeline {
  agent { 
    node { 
      label 'agentkubernetes'
    }
  }
  parameters {
    string(name: 'IMAGE_NAME', defaultValue: 'index-page', description: 'Docker image name')
    string(name: 'IMAGE_TAG',  defaultValue: '1.0', description: 'Docker image tag')
    string(name: 'namespace', defaultValue: 'test', description: 'Helm Namespace')
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
        sh """
          docker tag ${IMAGE_NAME}:${IMAGE_TAG} $ECR_REPO:${IMAGE_TAG}
          docker push $ECR_REPO:${IMAGE_TAG}
        """
      }
    }
    stage('Deploy the Application') {
      steps {
        sh """
        helm upgrade --install index-page helm/index-html \
          --set image.tag=${IMAGE_TAG} \
	  --namespace ${namespace} \
          --wait \
          --atomic \
          --timeout 5m
        """
      }
    } 
  }
}

