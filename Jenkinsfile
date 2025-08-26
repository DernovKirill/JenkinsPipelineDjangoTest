pipeline {
    agent {
        node {
            label 'dev'
        }
    }
    environment {
        PYTHON_VENV = 'python3.11-venv'
        LINT_FAIL_SCORE = '7.0'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'github-token', url: 'https://github.com/DernovKirill/django_tutorial.git'
            }
        }
        stage('Install Python Dependencies') {
            when {
                    expression {
                        return sh (
                            script: "dpkg -s ${env.PYTHON_VENV} >/dev/null 2>&1",
                            returnStatus: true
                        ) != 0
                    }
                }
            steps {
                sh 'sudo apt-get update && sudo apt-get install -y ${env.PYTHON_VENV}'
            }
        }
        stage('Install requirements') {
            steps {
                sh 'python3 -m venv .venv'
                sh '.venv/bin/pip install -r requirements.txt'
                sh '.venv/bin/pip install -r requirements-dev.txt'
            }
        }
        stage('Tests') {
            steps {
                sh '.venv/bin/python mysite/manage.py test polls'
            }
        }
        stage('Pylint check') {
            steps {
                sh """
                    .venv/bin/pylint --load-plugins=pylint_django mysite/ \
                        --output-format=json --fail-under=${env.LINT_FAIL_SCORE} \
                        > pylint-report.json
                """
            }
        }
        stage('OWASP Dependency-Check') {
            steps {
                sh """
                    docker run --rm \
                      -v $(pwd):/src \
                      owasp/dependency-check \
                      --scan /src \
                      --format "HTML" \
                      --out /src \
                      --enableExperimental
                """
            }
        }
        stage('Bandit check') {
            steps {
                sh '.venv/bin/bandit -r mysite/ -f json -o bandit-report.json -lll'
            }
        }
        stage('Check app') {
            steps {
                sh '.venv/bin/python mysite/manage.py check'
            }
        }
    }
}