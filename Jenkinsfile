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
        string(name: 'GIT_REPO') 
		defaultValue: 'https://github.com/awsdevopssri/Future.git')
        string(name: 'BRANCH', defaultValue: 'feature-ep2-task-1')
    }

    stages {

        stage('Checkout Code') {
            steps {
                cleanWs()
                git branch: "${params.BRANCH}",
                    url: "${params.GIT_REPO}"
            }
        }

        stage('Verify Tools') {
            steps {
                bat '''
                java -version
                mvn -version
                '''
            }
        }

        stage('Build Application') {
            steps {
                bat "mvn clean package"
            }
        }

        stage('Run Tests') {
            steps {
                bat "mvn test"
                junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml'
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {

        success {
            echo "✅ BUILD SUCCESS"
        }

        failure {
            echo "❌ BUILD FAILED → CLEANING WORKSPACE"
            deleteDir()
        }

        always {
            echo "Pipeline completed"
        }
    }
}