pipeline {
    agent any

    parameters {
        string(
            name: 'EMAIL_TO',
            defaultValue: 'nguyentien2001.tn@gmail.com',
            description: 'Recipient for stage notifications'
        )
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install') {
            steps {
                sh '''
                  mkdir -p reports
                  npm ci
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                  npm run build || echo "No build script defined; continuing."
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                  mkdir -p reports
                  npm test 2>&1 | tee reports/test-stage.log
                '''
            }
            post {
                always {
                    archiveArtifacts artifacts: 'reports/test-stage.log', allowEmptyArchive: true
                }
                success {
                    emailext(
                        to: params.EMAIL_TO,
                        subject: "[${env.JOB_NAME} #${env.BUILD_NUMBER}] TEST stage SUCCESS",
                        body: "Test stage finished successfully.\n\nSee attached log.",
                        attachmentsPattern: 'reports/test-stage.log',
                        attachLog: true
                    )
                }
                failure {
                    emailext(
                        to: params.EMAIL_TO,
                        subject: "[${env.JOB_NAME} #${env.BUILD_NUMBER}] TEST stage FAILURE",
                        body: "Test stage failed.\n\nSee attached log.",
                        attachmentsPattern: 'reports/test-stage.log',
                        attachLog: true
                    )
                }
            }
        }

        stage('Security Scan') {
            steps {
                sh '''
                  mkdir -p reports
                  npm audit --audit-level=high 2>&1 | tee reports/security-scan.log
                '''
            }
            post {
                always {
                    archiveArtifacts artifacts: 'reports/security-scan.log', allowEmptyArchive: true
                }
                success {
                    emailext(
                        to: params.EMAIL_TO,
                        subject: "[${env.JOB_NAME} #${env.BUILD_NUMBER}] SECURITY SCAN SUCCESS",
                        body: "Security scan completed successfully.\n\nSee attached log.",
                        attachmentsPattern: 'reports/security-scan.log',
                        attachLog: true
                    )
                }
                failure {
                    emailext(
                        to: params.EMAIL_TO,
                        subject: "[${env.JOB_NAME} #${env.BUILD_NUMBER}] SECURITY SCAN FAILURE",
                        body: "Security scan failed or found vulnerabilities.\n\nSee attached log.",
                        attachmentsPattern: 'reports/security-scan.log',
                        attachLog: true
                    )
                }
            }
        }
    }
}
