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

          HOST_JENKINS_HOME="$(docker volume inspect jenkins_home -f '{{.Mountpoint}}')"

          HOST_WORKSPACE="${WORKSPACE#/var/jenkins_home}"
          HOST_WORKSPACE="${HOST_JENKINS_HOME}${HOST_WORKSPACE}"

          echo "WORKSPACE=${WORKSPACE}"
          echo "HOST_WORKSPACE=${HOST_WORKSPACE}"
          test -f "${HOST_WORKSPACE}/package.json"

          docker run --rm \
            -e CI=true \
            -v "${HOST_WORKSPACE}":/app -w /app \
            node:7.8.0 \
            bash -lc "node -v && npm -v && npm install && npm test -- --watchAll=false"
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