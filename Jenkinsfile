pipeline {
    agent any
    environment {
        // SonarCloud configuration
        SONAR_SCANNER_HOME = tool 'SonarQubeScanner'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/IsurangiGuniyangodage/8.2CDevSecOp.git', branch: 'main'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage'
            }
        }
        
        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true'  // Continue even if audit finds vulnerabilities
            }
        }
        
        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    script {
                        // Download and set up SonarScanner
                        def scannerHome = tool 'SonarQubeScanner'
                        
                        // Run SonarScanner analysis
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=\${SONAR_PROJECT_KEY} \
                            -Dsonar.organization=\${SONAR_ORGANIZATION} \
                            -Dsonar.host.url=https://sonarcloud.io \
                            -Dsonar.login=\${SONAR_TOKEN} \
                            -Dsonar.sources=. \
                            -Dsonar.exclusions=node_modules/**,test/** \
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                        """
                    }
                }
            }
        }
    }
    
    post {
        always {
            echo 'SonarCloud analysis completed. Check https://sonarcloud.io for results.'
        }
        success {
            echo 'Pipeline succeeded! SonarCloud analysis was successful.'
        }
        failure {
            echo 'Pipeline failed! Check the logs for details.'
        }
    }
}
