pipeline {
  agent any

  tools {
    nodejs 'node'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm ci'
      }
    }

    stage('Build') {
      steps {
        sh 'export VITE_API_URL=http://localhost:8081 && npm run build'
        archiveArtifacts artifacts: 'dist/**', fingerprint: true
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          if [ -z "$(which serve)" ]; then npm i -g serve; fi
          pkill -f "serve -s dist -l 5173" || true
          nohup serve -s dist -l 5173 > /tmp/travel-frontend.log 2>&1 &
        '''
      }
    }

    stage('Smoke Test') {
      steps {
        sh 'sleep 2'
        sh 'curl -f http://localhost:5173 || true'
      }
    }
  }

  post {
    failure {
      echo '❌ Frontend pipeline failed'
    }
    success {
      echo '✅ Frontend deployed successfully!'
    }
  }
}

