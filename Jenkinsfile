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
    }

    post {
        success {
            echo 'Pipeline Succeeded!'
        }

        failure {
            echo 'Pipeline Failed!'
        }
    }
}