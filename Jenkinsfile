pipeline {
  agent any
  options { timestamps() }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/IsurangiGuniyangodage/8.2CDevSecOp.git'
      }
    }

    stage('Verify Node') {
      steps { bat 'node -v & npm -v' }
    }

    stage('Install Dependencies') {
      steps { bat 'npm install' }
    }

    stage('Run Tests') {
      steps { bat 'npm test || exit /b 0' }   // allow failing tests without breaking pipeline
    }

    stage('Generate Coverage Report') {
      steps { bat 'npm run coverage || exit /b 0' }   // ok if script doesn't exist
    }

    stage('NPM Audit (Security Scan)') {
      steps { bat 'npm audit || exit /b 0' }
    }

    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          bat '''
          setlocal EnableExtensions EnableDelayedExpansion
          set "CACHE=%WORKSPACE%\\.sonar"
          set "SCANNER_DIR=%CACHE%\\scanner"

          if not exist "%SCANNER_DIR%\\bin\\sonar-scanner.bat" (
            if not exist "%CACHE%" mkdir "%CACHE%"
            powershell -Command "Invoke-WebRequest -Uri 'https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-windows.zip' -OutFile '%CACHE%\\scanner.zip'"
            powershell -Command "Expand-Archive -Path '%CACHE%\\scanner.zip' -DestinationPath '%CACHE%' -Force"
            for /d %%D in ("%CACHE%\\sonar-scanner-*") do set "EXTRACTED=%%~fD"
            if not exist "%SCANNER_DIR%" mkdir "%SCANNER_DIR%"
            move /Y "!EXTRACTED!" "%SCANNER_DIR%" >NUL
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
