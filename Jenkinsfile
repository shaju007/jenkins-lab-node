pipeline {
  agent any

  environment {
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
          docker run --rm -v "$WORKSPACE":/workspace -w /workspace "$NODE_IMAGE" npm run lint
        '''
      }
    }

    stage('Test') {
      steps {
        sh '''
          set -euxo pipefail
          docker run --rm -v "$WORKSPACE":/workspace -w /workspace "$NODE_IMAGE" npm test
        '''
      }
    }

    stage('Build') {
      steps {
        sh '''
          set -euxo pipefail
          docker run --rm -v "$WORKSPACE":/workspace -w /workspace "$NODE_IMAGE" npm run build
        '''
      }
    }
  }

  post {
    always {
      junit testResults: 'reports/junit.xml', allowEmptyResults: true
      archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: true, fingerprint: true
      echo "Build URL: ${env.BUILD_URL}"
    }
  }
}
