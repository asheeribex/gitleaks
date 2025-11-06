pipeline {
  agent any

  stages {
    stage('Checkout Code') {
      steps {
        checkout scm
      }
    }

    stage('Run GitLeaks Scan') {
      steps {
        sh '''
          echo "🔍 Running GitLeaks scan for AWS secrets and other credentials..."

          # Run the scan and save a JSON report
          gitleaks detect --source=. \
            --report-format=json \
            --report-path=gitleaks-report.json \
            --verbose --redact --exit-code 1
        '''
      }
    }
  }

  post {
    always {
      echo "📄 Archiving GitLeaks report..."
      archiveArtifacts artifacts: 'gitleaks-report.json', fingerprint: true
    }

    failure {
      echo '❌ GitLeaks scan failed — potential secrets found! Check the console or the report.'
    }

    success {
      echo '✅ GitLeaks scan passed — no secrets detected.'
    }
  }
}
