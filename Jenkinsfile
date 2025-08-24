pipeline {
    agent {
        node {
            label 'dev'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'github-token', url: 'https://github.com/DernovKirill/django_tutorial.git'
            }
        }
        stage('Install Python Dependencies') {
            steps {
                sh 'sudo apt-get update && sudo apt-get install -y python3.11-venv'
                sh 'python3 -m venv .venv'
                sh 'chmod +x .venv/bin/activate'
                sh '.venv/bin/activate'
            }
        }
        stage('Build') {
            steps {
                sh '.venv/bin/pip install install -r requirements.txt'
            }
        }
    }
}