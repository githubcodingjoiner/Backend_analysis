pipeline {
    agent any

    tools {
        nodejs 'sonarnode' // Ensure 'sonarnode' is configured correctly in Jenkins Global Tool Configuration
    }

    environment {
        NODEJS_HOME = "${tool name: 'sonarnode'}" // Dynamically retrieve Node.js installation path
        SONAR_SCANNER_PATH = "C:\\Users\\Admin\\Downloads\\sonar-scanner-6.2.1.4610-windows-x64\\bin"
        PATH = "${env.NODEJS_HOME};${env.SONAR_SCANNER_PATH};${env.PATH}" // Append paths dynamically
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
                    npm install
                    '''
                }
            }
        }

        stage('Lint') {
            steps {
                bat '''
                npm run lint || (echo "Linting failed! Check logs for details." && exit /b)
                '''
            }
        }

        stage('Build') {
            steps {
                bat '''
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
        }
        always {
            cleanWs()
        }
    }
}
