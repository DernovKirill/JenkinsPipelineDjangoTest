pipeline {
    agent {
        node {
            label 'dev'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: '011445d4-237d-4890-98d5-12f1273f60be', url: 'https://github.com/DernovKirill/django_tutorial.git'
            }
        }
    }
}