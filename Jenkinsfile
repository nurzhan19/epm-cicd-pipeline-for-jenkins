pipeline {
  agent any
  options { timestamps() }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build & Test (Node 7.8.0)') {
      steps {
        sh '''
          docker run --rm -v "$PWD":/app -w /app node:7.8.0 \
            bash -lc 'node -v && npm -v && npm install && npm test'
        '''
      }
    }

    stage('Docker Build') {
      steps {
        script {
          def imageName = (env.BRANCH_NAME == 'main') ? 'nodemain:v1.0' : 'nodedev:v1.0'
          sh "docker build -t ${imageName} ."
        }
      }
    }
  }
}