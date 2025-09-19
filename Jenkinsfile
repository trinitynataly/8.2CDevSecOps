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
        script {
          def scannerHome = tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
          withSonarQubeEnv() {
            sh """
              echo "🔍 Sonar host: \$SONAR_HOST_URL"
              "${scannerHome}/bin/sonar-scanner" \
                -Dsonar.token=\${SONAR_AUTH_TOKEN}
            """
          }
        }
      }
    }
  }
}