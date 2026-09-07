pipeline {
  agent any
  environment {
    IMAGE      = "nishal3098/trend-app"
    AWS_REGION = "ap-south-1"
    CLUSTER    = "trend-cluster"
  }
  stages {
    stage('Checkout') {
      steps { git branch: 'main', url: 'https://github.com/nishal3098-eng/Trend.git' }
    }
    stage('Build') {
      steps { sh 'docker build -t $IMAGE:$BUILD_NUMBER -t $IMAGE:latest .' }
    }
    stage('Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'U', passwordVariable: 'P')]) {
          sh 'echo $P | docker login -u $U --password-stdin'
          sh 'docker push $IMAGE:$BUILD_NUMBER'
          sh 'docker push $IMAGE:latest'
        }
      }
    }
    stage('Deploy') {
      steps {
        sh 'aws eks update-kubeconfig --name $CLUSTER --region $AWS_REGION'
        sh 'kubectl apply -f k8s/deployment.yaml'
        sh 'kubectl apply -f k8s/service.yaml'
        sh 'kubectl set image deployment/trend-app trend-app=$IMAGE:$BUILD_NUMBER'
        sh 'kubectl rollout status deployment/trend-app'
      }
    }
  }
}