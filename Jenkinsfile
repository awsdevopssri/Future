pipeline {

    agent any

    triggers {
        githubPush()
        pollSCM('H/2 * * * *')
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        timestamps()
        retry(2)
    }

    parameters {
        string(name: 'GIT_REPO',
            defaultValue: 'https://github.com/awsdevopssri/Future.git',
            description: 'GitHub Repository URL')

        string(name: 'BRANCH',
            defaultValue: 'feature-ep2-task-1')
    }

    stages {

        stage('Install Python (if not exists)') {
            steps {
                bat '''
                python --version >nul 2>&1
                IF %ERRORLEVEL% NEQ 0 (
                    echo Python not found. Installing...

                    powershell -Command "Invoke-WebRequest -Uri https://www.python.org/ftp/python/3.12.9/python-3.12.9-amd64.exe -OutFile python-installer.exe"

                    python-installer.exe /quiet InstallAllUsers=1 PrependPath=1 Include_test=0

                    echo Python Installed
                ) ELSE (
                    echo Python already installed
                )
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

        stage('Verify Python') {
            steps {
                bat '''
                echo Checking Python
                python --version

                echo Checking pip
                python -m ensurepip
                python -m pip --version
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    if (fileExists('requirements.txt')) {
                        bat "python -m pip install -r requirements.txt"
                    } else {
                        echo "No requirements.txt found → Skipping"
                    }
                }
            }
        }

        stage('Run Application') {
            steps {
                script {
                    if (fileExists('app.py')) {
                        bat "python app.py"
                    } else {
                        echo "No app.py found → Skipping"
                    }
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    if (fileExists('test_app.py')) {
                        bat "python -m unittest test_app.py"
                    } else {
                        echo "No tests found → Skipping"
                    }
                }
            }
        }
    }

    post {

        success {
            echo "=================================="
            echo "✅ BUILD SUCCESS"
            echo "Application executed successfully"
            echo "=================================="
        }

        failure {
            echo "=================================="
            echo "❌ BUILD FAILED → DESTROYING EVERYTHING"
            echo "=================================="
            deleteDir()
        }

        always {
            echo "Pipeline completed"
        }
    }
}