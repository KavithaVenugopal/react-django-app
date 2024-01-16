pipeline {
    agent {
        docker {
            image 'python:3.9'
        }
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    //  Docker commands
                    sh '/var/lib/docker build -t reactdjan1 .'
                }
            }
        }

        stage('Run Tests in Docker') {
            steps {
                script {
                    // Run tests in the Docker container
                    // Explicitly set the PATH to include the directory where Docker is installed
                    withEnv(['PATH=/var/lib:$PATH']) {
                        docker.image('reactdjan1').inside {
                            sh 'pip install --upgrade pip && pip install -r requirements.txt'
                            sh 'pytest tests'
                        }
                    }
                }
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
            error('Tests failed! Build marked as FAILURE.')
        }
    }
}

