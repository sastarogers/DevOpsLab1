pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv .venv
                    .venv/bin/pip install --upgrade pip
                    .venv/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Lint') {
            steps {
                sh '''
                    .venv/bin/flake8 app.py
                '''
            }
        }

        stage('Unit Tests') {
            steps {
                sh '''
                    .venv/bin/pytest test_app.py -v
                '''
            }
        }

        stage('Package') {
            steps {
                sh '''
                    tar -czf app-${GIT_COMMIT}.tar.gz \
                        app.py \
                        test_app.py \
                        requirements.txt
                '''
            }
        }

        stage('Publish to S3') {
            steps {
                sh '''
                    aws s3 cp \
                    app-${GIT_COMMIT}.tar.gz \
                    s3://devopslab1-atharva-2026/
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully."
            echo "Artifact: app-${GIT_COMMIT}.tar.gz"
        }

        failure {
            echo "Pipeline failed. Check the stage logs."
        }
    }
}
