pipeline {
    agent {
        node {
            label 'dev'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/DernovKirill/django_tutorial.git'
            }
        }
    }
}