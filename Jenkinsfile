pipeline {

    agent any

    environment {
        PYTHON_HOME = "C:\\Program Files\\Python312"
        PATH = "${PYTHON_HOME};${PYTHON_HOME}\\Scripts;${env.PATH}"
    }

    stages {

        stage('Verify Correct Python') {
            steps {
                bat '''
                echo Checking Python Path
                where python

                echo Version
                python --version

                echo Checking pip
                python -m pip --version
                '''
            }
        }

        stage('Checkout Code') {
            steps {
                cleanWs()
                git branch: "feature-ep2-task-1",
                    url: "https://github.com/awsdevopssri/Future.git"
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