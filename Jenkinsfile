pipeline {
    agent any

    environment {
        SONARQUBE = 'MySonarQubeServer'
        IMAGE_NAME = 'node-api'
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t node-api .'
            }
        }



stage('Test') {
    steps {
        sh 'npm install'
        sh 'npm test || exit 0'
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
        sh "trivy image node-api || exit 0"
    }
}

stage('Deploy') {
    steps {
        sh 'docker-compose down || exit 0'
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
