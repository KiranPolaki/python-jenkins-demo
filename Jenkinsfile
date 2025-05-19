pipeline {
    agent any

    environment {
        VENV = 'venv'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup') {
            steps {
                sh '''
                    python3 -m venv $VENV
                    . $VENV/bin/activate
                    pip install --upgrade pip
                    pip install flake8 pytest pytest-cov wheel
                '''
            }
        }

        stage('Lint') {
            steps {
                sh '''
                    . $VENV/bin/activate
                    flake8 app/ tests/
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    . $VENV/bin/activate
                    pytest --cov=app --junitxml=pytest-results.xml tests/
                '''
            }
            post {
                always {
                    junit 'pytest-results.xml'
                }
            }
        }

        stage('Build') {
            steps {
                sh '''
                    . $VENV/bin/activate
                    python setup.py bdist_wheel
                '''
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
