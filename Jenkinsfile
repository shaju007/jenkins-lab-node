pipeline {
  agent any

  options {
    timestamps()
    disableConcurrentBuilds()
  }

  stages {
    stage('Setup') {
      steps {
        sh '''
          set -euxo pipefail
          node --version
          npm --version
          git --version
        '''
      }
    }

    stage('Install') {
      steps {
        sh '''
          set -euxo pipefail
          npm ci --no-audit --no-fund || npm install --no-audit --no-fund
        '''
      }
    }

    stage('Lint') {
      steps {
        sh 'npm run lint'
      }
    }

    stage('Test') {
      steps {
        sh 'npm test'
      }
    }

    stage('Build') {
      steps {
        sh 'npm run build'
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
