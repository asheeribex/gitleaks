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
                    gitleaks git --source . --report-path gitleaks-report.json --exit-code 1
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
            echo ":x: Gitleaks detected secrets! Check gitleaks-report.json for details."
        }
        success {
            echo ":white_check_mark: No secrets found by Gitleaks."
        }
    }
}



