pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Debug') {
      steps {
        withEnv(["PATH+HOMEBREW=/opt/homebrew/bin"]) {
          sh '''
            echo "PATH=$PATH"
            which node || true
            node -v || true
            which npm || true
            npm -v || true
          '''
        }
      }
    }

    stage('Build') {
      steps {
        withEnv(["PATH+HOMEBREW=/opt/homebrew/bin"]) {
          // prefer ci for reproducible installs
          sh 'npm ci || npm install'
        }
      }
    }

    stage('Test') {
      steps {
        withEnv(["PATH+HOMEBREW=/opt/homebrew/bin"]) {
          // ensure executable bit (safe even if already executable)
          sh 'chmod +x jenkins/scripts/test.sh || true'
          sh './jenkins/scripts/test.sh'
        }
      }
    }

    stage('Archive') {
      steps {
        // archive React build if present; allows empty archive so job doesn't fail
        archiveArtifacts artifacts: 'build/**', allowEmptyArchive: true, fingerprint: true
      }
    }
  }

  post {
    always {
      echo "Build finished with status: ${currentBuild.currentResult}"
    }
    success { echo 'Pipeline succeeded' }
    failure { echo 'Pipeline failed — check console output' }
  }
}
