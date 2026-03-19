pipeline {

    agent any

    environment {
        PYTHON_HOME = "C:\\Program Files\\Python312"
        PATH = "${PYTHON_HOME};${PYTHON_HOME}\\Scripts;${env.PATH}"
    }

    stages {

        stage('Install Python if missing') {
            steps {
                bat '''
                python --version >nul 2>&1
                IF %ERRORLEVEL% NEQ 0 (
                    echo Installing Python...

                    powershell -Command "Invoke-WebRequest -Uri https://www.python.org/ftp/python/3.12.9/python-3.12.9-amd64.exe -OutFile python-installer.exe"

                    python-installer.exe /quiet InstallAllUsers=1 PrependPath=1 Include_test=0

                    echo Python Installed
                ) ELSE (
                    echo Python already installed
                )
                '''
            }
        }

        stage('Verify Python') {
            steps {
                bat '''
                where python
                python --version

                python -m pip --version
                '''
            }
        }

        stage('Checkout Code') {
            steps {
                cleanWs()
                git branch: "${params.BRANCH}",
                    url: "${params.GIT_REPO}"
            }
        }

        stage('Install Dependencies') {
            steps {
                bat "python -m pip install -r requirements.txt"
            }
        }

        stage('Run App') {
            steps {
                bat "python app.py"
            }
        }

        stage('Run Tests') {
            steps {
                bat "python -m unittest test_app.py"
            }
        }
    }

    post {
        success {
            echo "✅ BUILD SUCCESS"
        }
        failure {
            echo "❌ BUILD FAILED → DESTROYING EVERYTHING"
            deleteDir()
        }
    }
}