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
                        sh '''
                            set -e
                            
                            # Install required tools
                            if ! command -v unzip >/dev/null 2>&1; then
                                apt-get update -y && apt-get install -y unzip curl
                            fi

                            # Download and setup SonarScanner
                            SCAN_ZIP="sonar-scanner-cli-5.0.1.3006-linux.zip"
                            SCAN_DIR="sonar-scanner-5.0.1.3006"
                            
                            echo "Downloading SonarScanner..."
                            rm -f "$SCAN_ZIP"
                            curl -L -o "$SCAN_ZIP" "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/$SCAN_ZIP"
                            
                            echo "Extracting SonarScanner..."
                            rm -rf "$SCAN_DIR"
                            unzip -q "$SCAN_ZIP"
                            
                            echo "Making SonarScanner executable..."
                            chmod +x "$SCAN_DIR/bin/sonar-scanner"
                            
                            echo "Running SonarCloud analysis..."
                            # Use absolute path to avoid PATH issues
                            ./"$SCAN_DIR"/bin/sonar-scanner \
                                -Dsonar.projectKey=IsurangiGuniyangodage_8.2CDevSecOp \
                                -Dsonar.organization=isurangiguniyangodage \
                                -Dsonar.host.url=https://sonarcloud.io \
                                -Dsonar.login="$SONAR_TOKEN" \
                                -Dsonar.sources=. \
                                -Dsonar.exclusions=node_modules/**,test/** \
                                -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
                                -Dsonar.projectName="NodeJS Goof Vulnerable App" \
                                -Dsonar.sourceEncoding=UTF-8
                            
                            echo "SonarCloud analysis completed successfully!"
                        '''
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
