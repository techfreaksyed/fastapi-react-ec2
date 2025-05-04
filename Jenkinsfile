0pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'react-app'
        REPO_URL = 'https://github.com/techfreaksyed/fastapi-react-ec2'
        TARGET_FOLDER = 'frontend'
    }

    stages {
        stage('Check and Clone Repository') {
            steps {
            script {
                if (fileExists('repo')) {
                echo "Repository already exists. Skipping clone."
                } else {
                sh "git clone ${REPO_URL} repo"
                }
            }
            }
        }

        stage('Navigate to Folder and Execute Commands') {
            steps {
                dir("repo/${TARGET_FOLDER}") {
                    sh 'npm install'
                    sh 'npm test'
                }
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
