pipeline {
  agent any

  environment {
    HOST_PROJECT_DIR = '/Users/sshaju/jenkins-lab-node'
    NODE_IMAGE = 'node:20-alpine'
  }

  stages {
    stage('Setup') {
      steps {
        sh '''
          set -euxo pipefail
          docker --version
          docker ps
          docker pull "$NODE_IMAGE"
        '''
      }
    }

    stage('Lint') {
      steps {
        sh '''
          set -euxo pipefail
          docker run --rm \
            -v "$HOST_PROJECT_DIR":/workspace \
            -w /workspace \
            "$NODE_IMAGE" \
            sh -lc "test -f package.json && npm run lint"
        '''
      }
    }

    stage('Test') {
      steps {
        sh '''
          set -euxo pipefail
          docker run --rm \
            -v "$HOST_PROJECT_DIR":/workspace \
            -w /workspace \
            "$NODE_IMAGE" \
            sh -lc "test -f package.json && npm test"
        '''
      }
    }

    stage('Build') {
      steps {
        sh '''
          set -euxo pipefail
          docker run --rm \
            -v "$HOST_PROJECT_DIR":/workspace \
            -w /workspace \
            "$NODE_IMAGE" \
            sh -lc "test -f package.json && npm run build"
        '''
      }
    }
  }

  post {
    success {
      echo "Pipeline finished successfully: ${env.BUILD_URL}"
    }
    failure {
      echo "Pipeline failed: ${env.BUILD_URL}"
    }
    always {
         junit 'reports/junit.xml'
         archiveArtifacts artifacts: 'reports/**', fingerprint: true
         echo "Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
    }
  }
}
