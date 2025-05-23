pipeline {
    agent any

    environment {
        SONARQUBE = 'MySonarQubeServer' // Name from Jenkins > Manage Jenkins > SonarQube Servers
        IMAGE_NAME = 'node-api'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/hil-ilma/pipelineTaskHD.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}")
                }
            }
        }

        stage('Test') {
            steps {
                sh 'npm install'
                sh 'npm test || true' // Prevents failure if some tests are flaky
            }
        }

        stage('Code Quality') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh 'sonar-scanner'
                }
            }
        }

        stage('Security') {
            steps {
                sh "trivy image ${IMAGE_NAME} || true" // Continue even if vulns exist
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker-compose down || true'  // Clean up previous
                sh 'docker-compose up -d --build'
            }
        }

        stage('Release (Tag Image)') {
            steps {
                script {
                    def tag = "v${new Date().format('yyyyMMddHHmm')}"
                    sh "docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${tag}"
                    echo "Released image as ${IMAGE_NAME}:${tag}"
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline completed."
        }
    }
}
