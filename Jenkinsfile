pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/dsshrey15-bits/jenkins-test.git'
            }
        }

        stage('Setup Python Environment') {
            steps {
                sh '''
                    python3 -m venv .venv
                    . .venv/bin/activate
                    python -m pip install --upgrade pip
                    python -m pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    . .venv/bin/activate
                    python -m pytest -q
                '''
            }
        }

        stage('Check Docker Access') {
            steps {
                sh '''
                    if ! docker info >/dev/null; then
                        echo "ERROR: The Jenkins agent cannot access the Docker daemon."
                        echo "Check Docker socket permissions and the agent user's docker-group membership."
                        id
                        ls -l /var/run/docker.sock
                        exit 1
                    fi
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t aceest-app .'
            }
        }
    }
}

