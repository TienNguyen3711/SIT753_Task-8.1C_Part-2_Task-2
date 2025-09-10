pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'echo "Test log content" > test.log'
            }
            post {
                always {
                    emailext(
                        to: 'nguyentien2001.tn@gmail.com',
                        subject: "Test Stage - ${currentBuild.currentResult}",
                        body: "The Test stage has finished with status: ${currentBuild.currentResult}",
                        attachLog: true
                    )
                }
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Running security scan...'
                sh 'echo "Security scan log" > scan.log'
            }
            post {
                always {
                    emailext(
                        to: 'nguyentien2001.tn@gmail.com',
                        subject: "Security Scan Stage - ${currentBuild.currentResult}",
                        body: "The Security Scan stage has finished with status: ${currentBuild.currentResult}",
                        attachLog: true
                    )
                }
            }
        }
    }
}
