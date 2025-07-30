pipeline {
  agent any

  options {
    timestamps()
  }

  environment {
    PROJECT_TESTING    = 'devopssqa113-g1-testing'
    PROJECT_STAGING    = 'devopssqa113-g1-staging'
    PROJECT_PRODUCTION = 'devopssqa113-g1-production'
    FIREBASE_TOKEN     = credentials('firebase-token')
  }

  stages {
    stage('Build – Install Firebase Tools') {
      steps {
        ansiColor('xterm') {
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
            else
              echo "No package.json. Skipping npm install."
            fi
          '''
        }
      }
    }

    stage('Testing') {
      steps {
        ansiColor('xterm') {
          sh '''
            set -e
            echo "Deploying to TESTING: $PROJECT_TESTING"
            firebase deploy --token "$FIREBASE_TOKEN" --only hosting:testing --project "$PROJECT_TESTING"
          '''
        }
      }
    }

    stage('Staging') {
      steps {
        ansiColor('xterm') {
          sh '''
            set -e
            echo "Deploying to STAGING: $PROJECT_STAGING"
            firebase deploy --token "$FIREBASE_TOKEN" --only hosting:staging --project "$PROJECT_STAGING"
          '''
        }
      }
    }

    stage('Production') {
      when {
        expression { env.BRANCH_NAME == 'main' || env.GIT_BRANCH == 'origin/main' || env.GIT_BRANCH == 'main' }
      }
      steps {
        ansiColor('xterm') {
          sh '''
            set -e
            echo "Deploying to PRODUCTION: $PROJECT_PRODUCTION"
            firebase deploy --token "$FIREBASE_TOKEN" --only hosting:production --project "$PROJECT_PRODUCTION"
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
