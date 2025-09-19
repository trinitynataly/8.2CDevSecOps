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
        sh 'npm install'
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

    stage('Install SonarScanner') {
      steps {
        sh 'npm install -g sonar-scanner'
      }
    }

    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh '''
            # 🔍 DEBUG INFO
            echo "🔍 Current directory:"
            pwd
            echo "🔍 List of files:"
            ls -la

            echo "🔍 Is sonar-scanner installed?"
            which sonar-scanner || echo "sonar-scanner NOT found"

            echo "🔍 List of JavaScript files:"
            find . -name '*.js' || echo "No JS files found"

            echo "🔍 Check for coverage/lcov.info:"
            ls -l coverage/lcov.info || echo "NO COVERAGE FILE FOUND"

            # 📊 Run SonarCloud Analysis
            sonar-scanner \
              -Dsonar.projectKey=trinitynataly_8.2CDevSecOps \
              -Dsonar.organization=trinitynataly \
              -Dsonar.host.url=https://sonarcloud.io \
              -Dsonar.login="$SONAR_TOKEN" \
              -Dsonar.sources=. \
              -Dsonar.exclusions=node_modules/**,test/** \
              -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
              -Dsonar.projectName="NodeJS Goof Vulnerable App" \
              -Dsonar.sourceEncoding=UTF-8 \
              -Dsonar.verbose=true | tee sonar-verbose.log
          '''
        }
        archiveArtifacts artifacts: 'sonar-verbose.log', fingerprint: true, onlyIfSuccessful: false
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh 'npm audit || true'
      }
    }
  }
}

