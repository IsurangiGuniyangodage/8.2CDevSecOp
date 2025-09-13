pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/IsurangiGuniyangodage/8.2CDevSecOp.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh '''
                    # Install coverage tool if not available
                    if ! npm list nyc >/dev/null 2>&1; then
                        npm install --save-dev nyc
                    fi
                    
                    # Try to run mocha tests directly for coverage
                    npx nyc --reporter=lcov npx mocha test/ || echo "Coverage generation failed, continuing..."
                '''
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    script {
                        // Get the SonarQube Scanner tool path
                        def scannerHome = tool 'SonarQubeScanner'
                        
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                                -Dsonar.projectKey=IsurangiGuniyangodage_8.2CDevSecOp \
                                -Dsonar.organization=isurangiguniyangodage \
                                -Dsonar.host.url=https://sonarcloud.io \
                                -Dsonar.login="$SONAR_TOKEN" \
                                -Dsonar.sources=. \
                                -Dsonar.exclusions=node_modules/**,test/** \
                                -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
                                -Dsonar.projectName="NodeJS Goof Vulnerable App" \
                                -Dsonar.sourceEncoding=UTF-8
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed. Check SonarCloud dashboard for results: https://sonarcloud.io'
        }
    }
}
