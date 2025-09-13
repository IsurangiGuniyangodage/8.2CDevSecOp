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
                    # Install coverage tool if not available and generate coverage
                    if ! npm list nyc >/dev/null 2>&1; then
                        npm install --save-dev nyc
                    fi
                    npx nyc --reporter=lcov npm test || true
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
                withSonarQubeEnv('sonarcloud') {
                    sh '''
                        sonar-scanner \
                            -Dsonar.projectKey=IsurangiGuniyangodage_8.2CDevSecOp \
                            -Dsonar.organization=isurangiguniyangodage \
                            -Dsonar.sources=. \
                            -Dsonar.exclusions=node_modules/**,test/** \
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
                            -Dsonar.projectName="NodeJS Goof Vulnerable App" \
                            -Dsonar.sourceEncoding=UTF-8
                    '''
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
