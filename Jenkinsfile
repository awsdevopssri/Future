pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python Env') {
            steps {
                bat '''
                python -m venv venv
                venv\\Scripts\\python -m pip install --upgrade pip

                if exist requirements.txt (
                    venv\\Scripts\\python -m pip install -r requirements.txt
                )
                '''
            }
        }

        stage('Run Application') {
            steps {
                bat '''
                venv\\Scripts\\python app.py
                '''
            }
        }

        stage('Run Tests') {
            steps {
                bat '''
                venv\\Scripts\\python -m unittest discover
                '''
            }
        }
    }
}