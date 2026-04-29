pipeline {
  agent any

  environment {
    KUBECONFIG = credentials('kubeconfig-creds')
  }

  stages {

    stage('Build Docker Image') {
      steps {
        bat 'docker build -t myapp:latest .'
      }
    }

    stage('Push Image to Docker Hub') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'docker-creds',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          bat '''
            docker login -u %DOCKER_USER% -p %DOCKER_PASS%
            docker tag myapp:latest %DOCKER_USER%/myapp:latest
            docker push %DOCKER_USER%/myapp:latest
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        bat '''
          kubectl apply -f k8s
          kubectl rollout restart deployment myapp
        '''
      }
    }
  }
}
