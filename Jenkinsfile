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
          sh 'npm ci || npm install'
        }
      }
    }

    stage('Test') {
      steps {
        withEnv(["PATH+HOMEBREW=/opt/homebrew/bin"]) {
          sh 'chmod +x jenkins/scripts/test.sh || true'
          sh './jenkins/scripts/test.sh'
        }
      }
    }

    stage('Archive') {
      steps {
        archiveArtifacts artifacts: 'build/**', allowEmptyArchive: true, fingerprint: true
      }
    }

    stage('Deliver') {
      steps {
        // ensure PATH and exec permission for deploy/kill scripts
        withEnv(["PATH+HOMEBREW=/opt/homebrew/bin"]) {
          sh 'chmod +x jenkins/scripts/deliver.sh || true'
          sh 'chmod +x jenkins/scripts/kill.sh || true'

          // run deliver script (should be idempotent and log its actions)
          sh './jenkins/scripts/deliver.sh'

          // wait for manual confirmation but don't block forever
          timeout(time: 30, unit: 'MINUTES') {
            input message: 'Finished using the web site? Click "Proceed" to continue or "Abort" to stop deployment.'
          }

          // after approval, run kill (or pm2 stop/restart)
          sh './jenkins/scripts/kill.sh'
        }
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
