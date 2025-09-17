pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/trinitynataly/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm ci || npm install --no-fund --no-audit'
      }
    }

    stage('Run Tests') {
      steps {
        sh 'npm test || true'
      }
    }

    stage('Generate Coverage') {
      steps {
        sh 'npm run coverage || true'
      }
    }

    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh '''
            set -e
            SCANNER_VERSION=5.0.1.3006
            SCANNER_DIR="$WORKSPACE/.scanner"
            mkdir -p "$SCANNER_DIR"
            curl -sSL -o "$SCANNER_DIR/sonar.zip" "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SCANNER_VERSION}-linux.zip"
            unzip -qo "$SCANNER_DIR/sonar.zip" -d "$SCANNER_DIR"
            export PATH="$SCANNER_DIR/sonar-scanner-${SCANNER_VERSION}-linux/bin:$PATH"

            # Ensure coverage exists (safe if already generated)
            [ -f coverage/lcov.info ] || npm test -- --coverage --ci || true

            # Run analysis; project/org from sonar-project.properties, token via CLI
            sonar-scanner -Dsonar.login="$SONAR_TOKEN"
          '''
        }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh 'npm audit || true'
      }
    }
  }
}
