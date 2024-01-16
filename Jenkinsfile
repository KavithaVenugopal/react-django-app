pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Run images') {
            steps {
                sh 'docker build -t kavitha/react .'
                sh 'docker run -p 8000:8000 -d kavitha/react'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pip install --no-cache-dir -r requirements.txt'
                sh 'pytest tests'
            }
        }
    }

    post {
        always {
            junit 'test-reports/**/*.xml'
        }
        success {
            echo 'All tests passed!'
        }
        failure {
            echo 'Tests failed! Build marked as FAILURE.'
            currentBuild.result = 'FAILURE'
        }
    }
}

