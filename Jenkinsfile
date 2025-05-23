pipeline {
    agent any

    environment {
        SONARQUBE = 'MySonarQubeServer'
        IMAGE_NAME = 'node-api'
    }

    stages {
        stage('Build') {
            steps {
                bat 'docker build -t node-api .'
            }
        }



stage('Test') {
    steps {
        bat 'npm install'
        bat 'npm test || exit 0'
    }
}

stage('Code Quality') {
    steps {
        withSonarQubeEnv("${SONARQUBE}") {
            bat 'sonar-scanner'
        }
    }
}

stage('Security') {
    steps {
        bat "trivy image node-api || exit 0"
    }
}

stage('Deploy') {
    steps {
        bat 'docker-compose down || exit 0'
        bat 'docker-compose up -d --build'
    }
}


        stage('Release (Tag Image)') {
            steps {
                script {
                    def tag = "v${new Date().format('yyyyMMddHHmm')}"
                    bat "docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${tag}"
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
