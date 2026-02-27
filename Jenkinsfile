pipeline {
  agent any

  options {
    timestamps()
    skipDefaultCheckout(true)
  }

  environment {
    CI = 'true'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build & Test (Node 7.8.0)') {
      steps {
        sh '''
          set -eux
          docker run --rm \
            -e CI=true \
            -v jenkins_home:/var/jenkins_home \
            -w "${WORKSPACE}" \
            node:7.8.0 \
            bash -lc 'node -v && npm -v && npm install && npm test -- --watchAll=false'
        '''
      }
    }

    stage('Docker Build') {
      steps {
        script {
          def imageName = (env.BRANCH_NAME == 'main') ? 'nodemain:v1.0' : 'nodedev:v1.0'
          sh """
            set -eux
            tar -C "${WORKSPACE}" -cf - . | docker build -t ${imageName} -
          """
        }
      }
    }
  }
}