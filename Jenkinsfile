pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                echo 'Building the application...'
                // Build tool example: Maven, Gradle, or npm
                sh 'mvn clean compile'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                // Test tool example: JUnit, Jest, pytest
                sh 'mvn test'
            }
            post {
                always {
                    // Send email notification after test stage
                    emailext (
                        to: 'developer@example.com',
                        subject: "TEST STAGE: ${currentBuild.currentResult} - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: """
                            <p>Test stage completed with status: <b>${currentBuild.currentResult}</b></p>
                            <p>Job: ${env.JOB_NAME}</p>
                            <p>Build Number: ${env.BUILD_NUMBER}</p>
                            <p>Build URL: ${env.BUILD_URL}</p>
                            <p>Please check the attached logs for details.</p>
                        """,
                        attachLog: true,
                        compressLog: true
                    )
                }
            }
        }
        
        stage('Security Scan') {
            steps {
                echo 'Running security scan...'
                // Security scan tool example: OWASP ZAP, SonarQube, Snyk
                sh 'mvn org.owasp:dependency-check-maven:check'
            }
            post {
                always {
                    // Send email notification after security scan stage
                    emailext (
                        to: 'developer@example.com',
                        subject: "SECURITY SCAN: ${currentBuild.currentResult} - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: """
                            <p>Security scan stage completed with status: <b>${currentBuild.currentResult}</b></p>
                            <p>Job: ${env.JOB_NAME}</p>
                            <p>Build Number: ${env.BUILD_NUMBER}</p>
                            <p>Build URL: ${env.BUILD_URL}</p>
                            <p>Please check the attached logs for details.</p>
                        """,
                        attachLog: true,
                        compressLog: true
                    )
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                // Deployment tool example: Docker, Kubernetes, Ansible
                sh 'docker build -t myapp .'
            }
        }
    }
    
    post {
        failure {
            // Send email on overall pipeline failure
            emailext (
                to: 'isuthilakshana@gmail.com',
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p>Pipeline failed!</p>
                    <p>Job: ${env.JOB_NAME}</p>
                    <p>Build Number: ${env.BUILD_NUMBER}</p>
                    <p>Build URL: ${env.BUILD_URL}</p>
                    <p>Please check the attached logs for details.</p>
                """,
                attachLog: true,
                compressLog: true
            )
        }
        
        success {
            // Send email on overall pipeline success
            emailext (
                to: 'isuthilakshana@gmail.com',
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p>Pipeline completed successfully!</p>
                    <p>Job: ${env.JOB_NAME}</p>
                    <p>Build Number: ${env.BUILD_NUMBER}</p>
                    <p>Build URL: ${env.BUILD_URL}</p>
                """,
                attachLog: false
            )
        }
    }
}
