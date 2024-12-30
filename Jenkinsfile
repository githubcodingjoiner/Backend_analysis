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

        stage('Install dependencies') {
            steps {
                retry(3) {
                    bat '''
                    set PATH=%NODEJS_HOME%;%PATH%
                    npm install
                    '''
                }
            }
        }
        
        stage('Build') {
            steps {
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm run build
                '''
            }
        }

        stage('SonarQube Analysis') {
        environment {
        SONAR_TOKEN = credentials('sonar-token')
    }
    steps {
        bat '''
        set PATH=%SONAR_SCANNER_PATH%;%PATH%
        sonar-scanner ^
        -Dsonar.projectKey=MERN_backend_pipeline ^
        -Dsonar.sources=. ^
        -Dsonar.host.url=http://localhost:9000 ^
        -Dsonar.login=%SONAR_TOKEN%
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
            echo 'This runs regardless of the result.'
        }
    }
}
