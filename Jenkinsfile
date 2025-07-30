pipeline {
  agent any

  options {
    timestamps()
    ansiColor('xterm')
  }

  environment {
    PROJECT_TESTING    = 'devopsSQA113-G1-Testing'
    PROJECT_STAGING    = 'devopsSQA113-G1-Staging'
    PROJECT_PRODUCTION = 'devopsSQA113-G1-Production'
  }

  stages {
    stage('Build – Install Firebase Tools') {
      steps {
        sh '''
          set -e
          echo "Node version:" && node -v || true
          echo "NPM version:" && npm -v || true
          echo "Installing Firebase CLI..."
          npm install -g firebase-tools
          echo "Firebase CLI version:"
          firebase --version
          if [ -f package.json ]; then
            echo "package.json found. Installing project deps..."
            npm ci || npm install
            # npm run build
          else
            echo "No package.json. Skipping npm install."
          fi
        '''
      }
    }

    stage('Testing') {
      steps {
        withCredentials([string(credentialsId: 'FIREBASE_TOKEN', variable: 'FIREBASE_TOKEN')]) {
          sh '''
            set -e
            echo "Deploying to TESTING: $PROJECT_TESTING"
            firebase deploy --only hosting --project "$PROJECT_TESTING"
          '''
        }
      }
    }

    stage('Staging') {
      steps {
        withCredentials([string(credentialsId: 'FIREBASE_TOKEN', variable: 'FIREBASE_TOKEN')]) {
          sh '''
            set -e
            echo "Deploying to STAGING: $PROJECT_STAGING"
            firebase deploy --only hosting --project "$PROJECT_STAGING"
          '''
        }
      }
    }

    stage('Production') {
      when { branch 'main' }
      steps {
        withCredentials([string(credentialsId: 'FIREBASE_TOKEN', variable: 'FIREBASE_TOKEN')]) {
          sh '''
            set -e
            echo "Deploying to PRODUCTION: $PROJECT_PRODUCTION"
            firebase deploy --only hosting --project "$PROJECT_PRODUCTION"
          '''
        }
      }
    }
  }

  post {
    success { echo '✅ Pipeline completed successfully.' }
    failure { echo '❌ Pipeline failed. Check logs above.' }
  }
}
