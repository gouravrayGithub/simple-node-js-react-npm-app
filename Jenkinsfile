pipeline {
  agent any
  tools { nodejs 'node18' } // <-- exact name from Global Tool Configuration
  stages {
    stage('Checkout') { steps { checkout scm } }

    stage('Build') {
      steps {
        sh 'npm ci || npm install'
      }
    }

    stage('Test') {
      steps {
        sh 'chmod +x jenkins/scripts/test.sh || true'
        sh './jenkins/scripts/test.sh'
      }
    }

    stage('Archive') {
      steps {
        sh 'npm run build --silent || echo "no build step"'
        archiveArtifacts artifacts: 'build/**', allowEmptyArchive: true, fingerprint: true
      }
    }
  }

  post {
    always { echo "Finished: ${currentBuild.currentResult}" }
    success { echo 'Pipeline succeeded' }
    failure { echo 'Pipeline failed — check console output' }
  }
}
