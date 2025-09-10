pipeline {
  agent any
  stages {
    stage('Debug') {
      steps {
        withEnv(["PATH+HOMEBREW=/opt/homebrew/bin"]) {
          sh 'echo "PATH=$PATH"; which node || true; node -v || true; which npm || true; npm -v || true'
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
  }
}
