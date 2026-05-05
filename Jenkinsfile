pipeline {
    agent any

    environment {
        SONAR_SCANNER_VERSION = '5.0.1.3006'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/laxminarsimha01/8.2CDevSecOps.git'
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
                        # Install unzip if missing
                        which unzip || (apt-get update && apt-get install -y unzip)

                        # Download SonarScanner CLI if not already present
                        if [ ! -d "sonar-scanner-${SONAR_SCANNER_VERSION}-linux" ]; then
                            curl -sSLo sonar-scanner.zip \
                              "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip"
                            unzip -q sonar-scanner.zip
                        fi

                        # Run the scanner
                        ./sonar-scanner-${SONAR_SCANNER_VERSION}-linux/bin/sonar-scanner \
                            -Dsonar.login=$SONAR_TOKEN
                    '''
                }
            }
        }
    }
}
