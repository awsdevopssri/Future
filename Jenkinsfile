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
            defaultValue: 'feature-ep2-task-1',
            description: 'Git Branch')
    }

    stages {

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
                python --version
                pip --version
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    if (fileExists('requirements.txt')) {
                        bat 'pip install -r requirements.txt'
                    } else {
                        echo "No requirements.txt found, skipping install"
                    }
                }
            }
        }

        stage('Run Application') {
            steps {
                script {
                    if (fileExists('app.py')) {
                        echo "Running Python Application"
                        bat 'python app.py'
                    } else {
                        echo "No app.py found"
                    }
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    if (fileExists('test_app.py')) {
                        echo "Running Tests"
                        bat 'python -m unittest discover'
                    } else {
                        echo "No test files found"
                    }
                }
            }
        }
    }

    post {

        success {
            echo "✅ BUILD SUCCESS (PYTHON)"
        }

        failure {
            echo "❌ BUILD FAILED → DESTROYING EVERYTHING"

            bat '''
            echo Cleaning Python cache...
            if exist __pycache__ rmdir /s /q __pycache__
            '''

            deleteDir()
        }

        always {
            echo "Pipeline completed"
        }
    }
}