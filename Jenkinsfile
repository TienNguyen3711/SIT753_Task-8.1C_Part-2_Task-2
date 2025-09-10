pipeline {
    agent any

    environment {
        RECIPIENTS = 'nguyentien2001.tn@gmail.com'
    }

    stages {
        stage('Install') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test || true'
            }
            post {
                always {
                    emailext(
                        to: "${RECIPIENTS}",
                        subject: "Test Stage - ${currentBuild.currentResult}",
                        body: "Test completed with status: ${currentBuild.currentResult}",
                        attachLog: true,
                        compressLog: true
                    )
                }
            }
        }

        stage('Security Scan') {
            steps {
                sh 'npm audit --json > audit.json || true'
                archiveArtifacts artifacts: 'audit.json', onlyIfSuccessful: false
            }
            post {
                always {
                    emailext(
                        to: "${RECIPIENTS}",
                        subject: "Security Scan - ${currentBuild.currentResult}",
                        body: "Security scan completed. See attached log and audit.json",
                        attachmentsPattern: 'audit.json',
                        attachLog: true,
                        compressLog: true
                    )
                }
            }
        }
    }
}
