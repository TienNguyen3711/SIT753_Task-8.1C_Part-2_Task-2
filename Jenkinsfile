pipeline {
  agent any

  options {
    timestamps()
    ansiColor('xterm')
  }

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
          set -euxo pipefail
          mkdir -p reports
          npm --version || true
          npm ci
        '''
      }
    }

    stage('Build') {
      steps {
        sh '''
          set -euxo pipefail
          npm run build || echo "No build script defined; continuing."
        '''
      }
    }

    stage('Test') {
      steps {
        sh '''
          set -euxo pipefail
          mkdir -p reports
          npm test 2>&1 | tee reports/test-stage.log
        '''
      }
      post {
        always {
          archiveArtifacts artifacts: 'reports/test-stage.log', allowEmptyArchive: true
          junit testResults: 'reports/**/*.xml', allowEmptyResults: true
        }
        success {
          sendEmail("TEST stage SUCCESS", "TEST stage finished successfully.", "reports/test-stage.log")
        }
        failure {
          sendEmail("TEST stage FAILURE", "TEST stage failed.", "reports/test-stage.log")
        }
      }
    }

    stage('Security Scan') {
      steps {
        sh '''
          set -euxo pipefail
          mkdir -p reports
          npm audit --audit-level=high 2>&1 | tee reports/security-scan.log
        '''
      }
      post {
        always {
          archiveArtifacts artifacts: 'reports/security-scan.log', allowEmptyArchive: true
        }
        success {
          sendEmail("SECURITY SCAN SUCCESS", "Security scan completed with no high-severity findings.", "reports/security-scan.log")
        }
        failure {
          sendEmail("SECURITY SCAN FAILURE", "Security scan failed or found high-severity vulnerabilities.", "reports/security-scan.log")
        }
      }
    }
  }
}

def sendEmail(String subjectSuffix, String bodyText, String attachment) {
  emailext(
    to: params.EMAIL_TO,
    subject: "[${env.JOB_NAME} #${env.BUILD_NUMBER}] ${subjectSuffix}",
    body: """\
${bodyText}

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
URL: ${env.BUILD_URL}

Attached: ${attachment} (and build log)
""",
    attachLog: true,
    compressLog: true,
    attachmentsPattern: attachment
  )
}
