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
                    pylint-json2html -f json -o pylint-report.html pylint-report.json
                """
                publishHTML(target: [
                    reportName : 'Pylint Report',
                    reportDir  : '',
                    reportFiles: 'pylint-report.html',
                    keepAll    : true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
        stage('OWASP Dependency-Check') {
            steps {
                sh """
                    mkdir -p ${WORKSPACE}/dependency-check-data
                    chmod 777 ${WORKSPACE}/dependency-check-data
                    mkdir -p ${WORKSPACE}/reports
                    chmod 777 ${WORKSPACE}/reports
                    docker run --rm \
                      -v ${WORKSPACE}:/src \
                      -v ${WORKSPACE}/dependency-check-data:/usr/share/dependency-check/data \
                      owasp/dependency-check \
                      --data /usr/share/dependency-check/data \
                      --project "DjangoTutorial" \
                      --scan /src \
                      --format "HTML" \
                      --out /src/reports \
                      --enableExperimental
                """
            }
        }
        stage('Dependency-Check Publish Report') {
            steps {
                publishHTML(target: [
                    reportName : 'OWASP Dependency-Check Report',
                    reportDir  : 'reports',
                    reportFiles: 'dependency-check-report.html',
                    keepAll    : true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
        stage('Bandit check') {
            steps {
                sh '.venv/bin/bandit -r mysite/ -f html -o bandit-report.html -lll'
                publishHTML(target: [
                    reportName : 'Bandit Security Check Report',
                    reportDir  : '',
                    reportFiles: 'bandit-report.html',
                    keepAll    : true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
        stage('Check app') {
            steps {
                sh '.venv/bin/python mysite/manage.py check'
            }
        }
    }
}