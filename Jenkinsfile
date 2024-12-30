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
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                retry(3) {
                    echo 'Installing dependencies...'
                    bat '''
                    set PATH=%NODEJS_HOME%;%PATH%
                    npm install
                    '''
                }
            }
        }

        stage('Lint') {
            steps {
                echo 'Running lint checks...'
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm run lint || echo "Linting errors detected. Review the output above."
                '''
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests...'
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm test || (echo "Tests failed!" && exit /b)
                '''
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm install @babel/plugin-proposal-private-property-in-object --save-dev
                npm run build || (echo "Build failed! Check logs for details." && exit /b)
                '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token')
            }
            steps {
                echo 'Running SonarQube analysis...'
                bat '''
                set PATH=%NODEJS_HOME%;%SONAR_SCANNER_PATH%;%PATH%
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
            cleanWs()
        }
    }
}
