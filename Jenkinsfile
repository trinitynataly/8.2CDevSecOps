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
 
    withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh '''
            echo "🔍 Running SonarCloud analysis..."
            sonar-scanner \
              -Dsonar.login=$SONAR_TOKEN
          '''
        }
  }
}
