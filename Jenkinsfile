pipeline {
    agent any

    environment {
        IMAGE_NAME = 'app-generator:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/app-generator/app-generator.git', branch: 'main'
            }
        }

        stage('Lint & Static Analysis') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install flake8
                    [ -f requirements.txt ] && pip install -r requirements.txt || echo "No requirements.txt found"
                    flake8 . --exclude=venv,.git --count --select=E9,F63,F7,F82 --show-source --statistics
                '''
            }
        }

        stage('Execute Tests') {
            steps {
                sh '''
                    . venv/bin/activate
                    pip install pytest

                    if [ -d tests ]; then
                        pytest tests
                    else
                        echo "No tests directory found"
                    fi
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    if [ -f Dockerfile ]; then
                        echo "Dockerfile found, building Docker image..."
                        docker build -t ${IMAGE_NAME} .
                    else
                        echo "No Dockerfile found, creating a basic Dockerfile..."
                        cat > Dockerfile <<EOF
FROM python:3.10-slim
WORKDIR /app
COPY . .
CMD ["python3", "-m", "http.server", "8000"]
EOF
                        docker build -t ${IMAGE_NAME} .
                    fi
                '''
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace and local Docker images...'
            sh '''
                docker rmi ${IMAGE_NAME} || true
            '''
        }

        success {
            echo 'Pipeline Succeeded!'
        }

        failure {
            echo 'Pipeline Failed!'
        }
    }
}