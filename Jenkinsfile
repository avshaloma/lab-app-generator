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

