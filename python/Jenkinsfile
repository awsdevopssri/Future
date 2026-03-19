pipeline {
    agent any

    triggers {
        pollSCM('H/2 * * * *')   // check repo every 2 minutes
    }

    options {
        retry(2)                          // retry build 2 times if failure
        timeout(time: 10, unit: 'MINUTES')
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check Python') {
            steps {
                bat '''
                echo Checking Python availability...

                python --version
                IF %ERRORLEVEL% NEQ 0 (
                    echo Python not found! Please install Python and add to PATH
                    exit /b 1
                )
                '''
            }
        }

        stage('Setup Python Env') {
            steps {
                bat '''
                echo Checking venv module...

                python -m venv venv
                IF %ERRORLEVEL% NEQ 0 (
                    echo venv not available, running without virtual environment...
                    exit /b 0
                )

                echo Activating virtual environment and installing dependencies...

                venv\\Scripts\\python -m pip install --upgrade pip

                IF exist requirements.txt (
                    venv\\Scripts\\python -m pip install -r requirements.txt
                ) ELSE (
                    echo No requirements.txt found
                )
                '''
            }
        }

        stage('Run Application') {
            steps {
                bat '''
                IF exist venv (
                    venv\\Scripts\\python app.py
                ) ELSE (
                    python app.py
                )
                '''
            }
        }

        stage('Run Tests') {
            steps {
                bat '''
                IF exist venv (
                    venv\\Scripts\\python -m unittest discover
                ) ELSE (
                    python -m unittest discover
                )
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Build SUCCESS'
        }
        failure {
            echo '❌ Build FAILED'
        }
        always {
            echo '📦 Cleaning workspace...'
        }
    }
}