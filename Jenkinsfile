pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv'
    }
    stages {
        stage('Setup') {
            steps {
                script {
                    // Create and activate virtual environment, then install dependencies
                    bat """
                        if not exist ${VIRTUAL_ENV} (
                            python -m venv ${VIRTUAL_ENV}
                        )
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        pip install --upgrade pip
                        pip install -r requirements.txt
                    """
                }
            }
        }
        stage('Lint') {
            steps {
                script {
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        flake8 app.py --exit-zero
                    """
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        set PYTHONPATH=%WORKSPACE%
                        pytest
                    """
                }
            }
        }
        stage('Coverage') {
            steps {
                script {
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        set PYTHONPATH=%WORKSPACE%
                        coverage run --source=. -m pytest
                        coverage report
                        coverage xml -o coverage.xml
                    """
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        bandit -r . -f xml -o bandit_report.xml --exit-zero
                    """
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo "Deploying application..."
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        python deploy.py
                    """
                }
            }
        }
    }
    post {
        always {
            cleanWs() // Clean workspace after the pipeline finishes
            publishCoverage adapters: [coberturaAdapter('coverage.xml')], sourceFileResolver: sourceFiles('NEVER_STORE')
        }
    }
}
