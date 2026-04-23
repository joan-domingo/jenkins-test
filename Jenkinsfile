pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/joan-domingo/jenkins-test.git'
            }
        }

        stage('Install') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                sh 'pytest || true'
            }
        }
    }
}