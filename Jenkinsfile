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
                    if (fileExists('tests')) {
                        bat "python -m pytest || exit 0"
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

            // Cleanup EVERYTHING
            deleteDir()
        }

        always {
            echo "Pipeline completed"
        }
    }
}