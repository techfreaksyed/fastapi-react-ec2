pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'react-app'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
            script {
                def imageExists = sh(
                script: "docker images -q ${DOCKER_IMAGE}",
                returnStdout: true
                ).trim()
                if (imageExists) {
                echo "Docker image ${DOCKER_IMAGE} already exists. Skipping build."
                } else {
                sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
            }
        }

        stage('Run Application') {
            steps {
            script {
                def containerExists = sh(
                script: "docker ps -a --filter 'name=react-app' --format '{{.Names}}' | grep -w react-app || true",
                returnStdout: true
                ).trim()
                if (containerExists) {
                echo "Container react-app already exists. Skipping run."
                } else {
                sh "docker run -d -p 3000:3000 --name react-app ${DOCKER_IMAGE}"
                }
            }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
    }
}