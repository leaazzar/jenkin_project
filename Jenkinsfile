pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv'
    }
    stages {
        stage('Check Python') {
            steps {
                script {
                    bat '''
                        python --version || (echo "Python is not installed or not in PATH" && exit 1)
                    '''
                }
            }
        }
        stage('Setup') {
            steps {
                script {
                    bat '''
                        if not exist venv (python -m venv venv)
                        call venv\\Scripts\\activate.bat
                        pip install --upgrade pip
                        pip install -r requirements.txt
                    '''
                }
            }
        }
        stage('Lint') {
            steps {
                script {
                    bat '''
                        call venv\\Scripts\\activate.bat
                        flake8 app.py --exit-zero
                    '''
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    bat '''
                        call venv\\Scripts\\activate.bat
                        pytest
                    '''
                }
            }
        }
        stage('Coverage') {
            steps {
                script {
                    bat '''
                        call venv\\Scripts\\activate.bat
                        coverage run --source=. -m pytest
                        coverage report
                        coverage xml -o coverage.xml
                    '''
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    bat '''
                        call venv\\Scripts\\activate.bat
                        bandit -r . -f xml -o bandit_report.xml --exit-zero
                    '''
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo "Deploying application..."
                    bat '''
                        call venv\\Scripts\\activate.bat
                        python deploy.py
                    '''
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
