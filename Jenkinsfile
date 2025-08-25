pipeline {
    agent {
        node {
            label 'dev'
        }
    }
    environment {
        PYTHON_VENV = 'python3.11-venv'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'github-token', url: 'https://github.com/DernovKirill/django_tutorial.git'
            }
        }
        stage('Install Python Dependencies') {
            steps {
                echo 'dpkg -s ${PYTHON_VENV} >/dev/null 2>&1'
                when {
                    expression {
                        return
                    }
                }
                sh 'sudo apt-get update && sudo apt-get install -y ${env.PYTHON_VENV}'
                sh 'python3 -m venv .venv'
                sh 'chmod +x .venv/bin/activate'
                sh '.venv/bin/activate'
                sh 'python3 -m pip install --upgrade pip'
            }
        }
        stage('Build') {
            steps {
                sh '.venv/bin/pip install -r requirements.txt'
            }
        }
        stage('Tests') {
            steps {
                sh 'python3 mysite/manage.py test polls'
            }
        }
    }
}