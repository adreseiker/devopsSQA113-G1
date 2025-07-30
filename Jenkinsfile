pipeline {
  agent any

  options {
    timestamps()
  }

  environment {
    PROJECT_TESTING    = 'devopsSQA113-G1-Testing'
    PROJECT_STAGING    = 'devopsSQA113-G1-Staging'
    PROJECT_PRODUCTION = 'devopsSQA113-G1-Production'
    // Token incrustado (solo para pruebas)
    FIREBASE_TOKEN     = '1//06ZRRY-gjmfl5CgYIARAAGAYSNwF-L9Irh3bHhjqS7x76tIAgDG8sE3i1x0woNUF2Pu-Ja9ZGFcuCUJrxBRt9oMAyT0OSbBDK5j4'
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
              # npm run build
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
            firebase deploy --token "$FIREBASE_TOKEN" --only hosting --project "$PROJECT_TESTING"
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
            firebase deploy --token "$FIREBASE_TOKEN" --only hosting --project "$PROJECT_STAGING"
          '''
        }
      }
    }

    stage('Production') {
      when { branch 'main' }
      steps {
        ansiColor('xterm') {
          sh '''
            set -e
            echo "Deploying to PRODUCTION: $PROJECT_PRODUCTION"
            firebase deploy --token "$FIREBASE_TOKEN" --only hosting --project "$PROJECT_PRODUCTION"
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
