
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup') {
            steps {
               
                sh 'python3 -m venv venv'
                sh '. venv/bin/activate'
                sh 'venv/bin/pip install -r requirements.txt'
            }
        }

        stage('Lint') {
            steps {
                sh 'venv/bin/flake8 app/ tests/'
            }
        }

        stage('Test') {
            steps {
                sh 'venv/bin/python3 -m pytest --cov=app tests/'
            }
            post {
                always {
                    sh 'venv/bin/python3 -m pytest --cov=app --cov-report=xml tests/'
                    junit 'venv/bin/pytest-results.xml'// Requires pytest-junit plugin
                }
            }
        }

        stage('Build') {
            steps {
                sh 'venv/bin/pip install wheel'
                sh 'venv/bin/python3 setup.py bdist_wheel'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'dist/*.whl', fingerprint: true
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to staging environment...'
                sh 'mkdir -p staging && cp dist/*.whl staging/'
            }
        }
    }
}
