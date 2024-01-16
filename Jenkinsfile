pipeline {
    agent any

    environment {
        DOCKER_CONTAINER_ID = ''
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Run images') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t kavitha/react .'

                    // Run Docker container and capture the container ID
                    DOCKER_CONTAINER_ID = sh(script: 'docker run -p 8000:8000 -d kavitha/react', returnStdout: true).trim()
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Install dependencies and run tests inside the Docker container
                    sh "docker exec ${DOCKER_CONTAINER_ID} pip install --no-cache-dir -r requirements.txt"
                    sh "docker exec ${DOCKER_CONTAINER_ID} pytest tests"
                }
            }
        }
    }

   post {
        always {
            script {
                // Print the contents of the workspace for debugging
                sh 'ls -R'

                // Find and print the test result files
                def testResultFiles = findFiles(glob: 'test-reports/**/*.xml')
                echo "Test result files found: ${testResultFiles.join(', ')}"
            }
            junit 'test-reports/**/*.xml'
        }
        success {
            echo 'All tests passed!'
        }
        failure {
            echo 'Tests failed! Build marked as FAILURE.'
            error('Tests failed! Build marked as FAILURE.')
        }
        cleanup {
            // Stop and remove the Docker container after the tests
            script {
                sh "docker stop ${DOCKER_CONTAINER_ID}"
                sh "docker rm ${DOCKER_CONTAINER_ID}"
            }
        }
    }
}
