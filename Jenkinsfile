pipeline {
  agent any
  options { timestamps() }

  environment {
    IMAGE = 'afk24cmd/dummyrepo'
    TAG   = "${env.BUILD_NUMBER}"      // or use: TAG = "${env.GIT_COMMIT.take(7)}"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t $IMAGE:$TAG .'
      }
    }

    stage('Login & Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DHU', passwordVariable: 'DHP')]) {
          sh '''
            echo "$DHP" | docker login -u "$DHU" --password-stdin
            docker push $IMAGE:$TAG
            docker tag  $IMAGE:$TAG $IMAGE:latest
            docker push $IMAGE:latest
          '''
        }
      }
    }
  }

  post {
    success {
      echo "Pushed: $IMAGE:$TAG and $IMAGE:latest"
    }
    failure {
      echo "Build or push failed—check logs."
    }
  }
}
