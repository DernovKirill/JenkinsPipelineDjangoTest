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
        stage('Build') {
            steps {
                sh 'python3 -m venv .venv'
                sh '.venv/bin/activate'
            }
        }
    }
}