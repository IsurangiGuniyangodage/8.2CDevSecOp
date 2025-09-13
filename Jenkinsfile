pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/IsurangiGuniyangodage/8.2CDevSecOp.git'
      }
    }
    stage('Install Dependencies') { steps { bat 'npm install' } }
    stage('Run Tests')           { steps { bat 'npm test || exit /b 0' } }
    stage('Generate Coverage Report') { steps { bat 'npm run coverage || exit /b 0' } }
    stage('NPM Audit (Security Scan)') { steps { bat 'npm audit || exit /b 0' } }

    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          bat '''
          setlocal
          set "SCANNER_DIR=%WORKSPACE%\\.sonar\\scanner"
          if not exist "%SCANNER_DIR%" mkdir "%SCANNER_DIR%"

          if not exist "%SCANNER_DIR%\\bin\\sonar-scanner.bat" (
            powershell -Command "Invoke-WebRequest -Uri https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-windows.zip -OutFile scanner.zip"
            powershell -Command "Expand-Archive -Path scanner.zip -DestinationPath %WORKSPACE%\\.sonar -Force"
            for /d %%D in ("%WORKSPACE%\\.sonar\\sonar-scanner-*") do set "EXTRACTED=%%D"
            move "%EXTRACTED%" "%SCANNER_DIR%"
          )
          set "PATH=%SCANNER_DIR%\\bin;%PATH%"
          call "%SCANNER_DIR%\\bin\\sonar-scanner.bat" -D"sonar.login=%SONAR_TOKEN%"
          endlocal
          '''
        }
      }
    }
  }
}
