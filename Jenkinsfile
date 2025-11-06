pipeline {
    agent any
    options {
        timeout(time: 30, unit: 'MINUTES')
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: env.BRANCH_NAME]],
                    extensions: [
                        [$class: 'CloneOption',
                            depth: 1,      // ← THIS IS THE KEY: shallow clone
                            shallow: true,
                            honorRefspec: true
                        ],
                        [$class: 'CleanCheckout']
                    ],
                    userRemoteConfigs: scm.userRemoteConfigs
                ])
            }
        }
        stage('Gitleaks Scan') {
            steps {
                sh '''
                    echo "Running Gitleaks scan (shallow clone - only latest commit)..."
                    # Modern command (v8.19+)
                    gitleaks git \
                        --report-path gitleaks-report.json \
                        --report-format json \
                        --redact \
                        --verbose \
                        --exit-code 1
                '''
            }
        }
        stage('Archive Report') {
            steps {
                archiveArtifacts artifacts: 'gitleaks-report.json', allowEmptyArchive: true, fingerprint: true
            }
        }
    }
    post {
        always {
            recordIssues(
                enabledForFailure: true,
                tool: issues(name: 'Gitleaks', pattern: 'gitleaks-report.json')
            )
        }
        failure {
            echo ":x: Gitleaks found secrets in the latest commit!"
        }
        success {
            echo ":white_check_mark: No new secrets detected by Gitleaks"
        }
    }
}
