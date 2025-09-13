pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/IsurangiGuniyangodage/8.2CDevSecOp.git'
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
        sh 'npm run coverage || true'
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
          sh '''
            set -e
            SCANNER_DIR="$WORKSPACE/.sonar/scanner"
            mkdir -p "$SCANNER_DIR"

            if [ ! -x "$SCANNER_DIR/bin/sonar-scanner" ]; then
              curl -L -o scanner.zip \
                https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip
              unzip -q scanner.zip -d "$WORKSPACE/.sonar"
              mv "$WORKSPACE/.sonar/sonar-scanner-5.0.1.3006-linux" "$SCANNER_DIR"
            fi

            export PATH="$SCANNER_DIR/bin:$PATH"
            sonar-scanner -Dsonar.login="$SONAR_TOKEN"
          '''
        }
      }
    }
  }
}
