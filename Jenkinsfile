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
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    parameters {

        string(
            name: 'GIT_REPO',
            defaultValue: 'https://github.com/awsdevopssri/Future.git',
            description: 'GitHub Repository URL'
        )

        string(
            name: 'BRANCH',
            defaultValue: 'feature-ep2-task-1',
            description: 'Git Branch'
        )

        string(
            name: 'MAIN_CLASS',
            defaultValue: 'com.example.App',
            description: 'Java Main Class'
        )

        choice(
            name: 'MAVEN_GOAL',
            choices: ['clean package','clean install','package'],
            description: 'Maven Build Goal'
        )

        booleanParam(
            name: 'CLEAN_ON_FAIL',
            defaultValue: true,
            description: 'Cleanup workspace if build fails'
        )
    }

    environment {
        APP_NAME = "java-devops-app"
    }

    stages {

        stage('Checkout Code') {
            steps {

                script {
                    if (fileExists('.git')) {
                        echo "Repository already exists → Pulling latest code"

                        bat "git pull origin ${params.BRANCH}"
                    } else {
                        echo "Cloning fresh repository"

                        git branch: "*/${params.BRANCH}",
                            url: "${params.GIT_REPO}"
                    }
                }
            }
        }

        stage('Build Info') {
            steps {

                echo "==================================="
                echo "Build Number: ${env.BUILD_NUMBER}"
                echo "Branch: ${params.BRANCH}"
                echo "==================================="

                bat 'git log -1 --oneline'
            }
        }

        stage('Verify Tools') {
            steps {
                bat '''
                echo Checking Java
                java -version

                echo Checking Maven
                mvn -version
                '''
            }
        }

        stage('Build Application') {
            steps {
                echo "Running Maven Build"

                bat "mvn ${params.MAVEN_GOAL}"
            }
        }

        stage('Run Tests') {
            steps {

                bat 'mvn test'
            }
            post {
                always {
                    junit allowEmptyResults: true,
                          testResults: 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Run Application') {
            steps {
                script {

                    if (fileExists('target')) {

                        echo "Running Application"

                        bat "java -cp target\\*.jar ${params.MAIN_CLASS}"

                    } else {

                        echo "No JAR found, skipping run"
                    }
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {

        success {
            echo "=================================="
            echo " BUILD SUCCESSFUL "
            echo " ${env.APP_NAME} executed successfully"
            echo "=================================="
        }

        failure {
            echo "=================================="
            echo " BUILD FAILED → CLEANING EVERYTHING "
            echo "=================================="

            script {
                if (params.CLEAN_ON_FAIL) {
                    deleteDir()
                }
            }
        }

        always {
            echo "Pipeline execution completed"
        }
    }
}