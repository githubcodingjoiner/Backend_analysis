pipeline {
    agent any

    tools {
        nodejs 'sonarnode'
    }

    environment {
        NODEJS_HOME = "C:\\Program Files\\nodejs"
        SONAR_SCANNER_PATH = "C:\\Users\\Admin\\Downloads\\sonar-scanner-6.2.1.4610-windows-x64\\bin"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                retry(3) {
                    bat '''
                    set PATH=%NODEJS_HOME%;%PATH%
                    npm install
                    '''
                }
            }
        }

        stage('Lint') {
            steps {
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm run lint || (echo "Linting failed! Check logs for details." && exit /b)
                '''
            }
        }

        stage('Build') {
            steps {
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm install @babel/plugin-proposal-private-property-in-object --save-dev
                npm run build || (echo "Build failed! Check logs for details." && exit /b)
                '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token') // Ensure this credential exists in Jenkins
            }
            steps {
                bat '''
                set PATH=%SONAR_SCANNER_PATH%;%PATH%
                sonar-scanner ^
                -Dsonar.projectKey=MERN_backend_pipeline ^
                -Dsonar.sources=. ^
                -Dsonar.host.url=http://localhost:9000 ^
                -Dsonar.login=%SONAR_TOKEN% || (echo "SonarQube Analysis failed! Check logs for details." && exit /b)
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for errors.'
            archiveArtifacts artifacts: '**/*.log', allowEmptyArchive: true
        }
        always {
            cleanWs()
        }
    }
}
