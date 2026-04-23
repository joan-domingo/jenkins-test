pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/joan-domingo/jenkins-test.git'
            }
        }

        stage('Install') {
            steps {
                sh '''
                    apt-get update && apt-get install -y python3 python3-pip
                    pip3 install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh 'pytest || true'
            }
        }
    }
}