pipeline {
  agent any

  stages {
    stage('Checkout Code') {
      steps {
        checkout scm
      }
    }

    stage('Run Gitleaks Scan') {
            steps {
                sh '''
                    echo "Running Gitleaks scan..."
                    gitleaks dir --source . --report-path gitleaks1-report.json --exit-code 1
                '''
            }
        }

        stage('Post Scan Report') {
            steps {
                archiveArtifacts artifacts: 'gitleaks-report.json', allowEmptyArchive: true
            }
        }
    }

    post {
        failure {
            echo "❌ Gitleaks detected the secrets! Check gitleaks-report.json for details."
        }
        success {
            echo "✅ No secrets found by Gitleaks."
        }
    }
}

