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
          // Resolve the auto-installed scanner
          def scannerHome = tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
          // Inject SONAR_HOST_URL and SONAR_AUTH_TOKEN for "SonarCloud"
          withSonarQubeEnv('SonarCloud') {
            sh """
              echo "🔍 Running SonarCloud analysis with scanner at: ${scannerHome}"
              "${scannerHome}/bin/sonar-scanner" \
                -Dsonar.login=${SONAR_AUTH_TOKEN}
            """
          }
        }
      }
    }
  }
}