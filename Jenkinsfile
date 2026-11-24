pipeline {
    agent any

    environment {
        KUBECONFIG = '/var/jenkins_home/.kube/config'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/dsshrey15-bits/DevOpsAssignment-2026ht66054.git'
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

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'
                    withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
                        withSonarQubeEnv('SonarQube') {
                            withEnv(["SONAR_SCANNER_HOME=${scannerHome}"]) {
                                sh '''
                                    "$SONAR_SCANNER_HOME/bin/sonar-scanner"
                                '''
                            }
                        }
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t aceest-app .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKERHUB_USERNAME',
                    passwordVariable: 'DOCKERHUB_PASSWORD'
                )]) {
                    sh '''
                        set -eu
                        export DOCKER_CONFIG="$(mktemp -d)"
                        trap 'rm -rf "$DOCKER_CONFIG"' EXIT

                        IMAGE_REPOSITORY='docker.io/dsshrey15bits/devops-assignment-2'
                        printf '%s' "$DOCKERHUB_PASSWORD" | docker login docker.io \
                            --username "$DOCKERHUB_USERNAME" --password-stdin
                        docker tag aceest-app "$IMAGE_REPOSITORY:latest"
                        docker tag aceest-app "$IMAGE_REPOSITORY:$BUILD_NUMBER"
                        docker push "$IMAGE_REPOSITORY:latest"
                        docker push "$IMAGE_REPOSITORY:$BUILD_NUMBER"
                    '''
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh '''
                    set -eu
                    export PATH="/var/jenkins_home/bin:$PATH"

                    command -v kubectl >/dev/null 2>&1 || {
                        echo "kubectl must be installed on the Jenkins agent" >&2
                        exit 1
                    }
                    kubectl config use-context minikube
                    kubectl get --raw=/version >/dev/null

                    kubectl apply -f k8s/deployment.yaml

                    kubectl rollout restart deployment/aceest-fitness-gym
                    kubectl rollout status deployment/aceest-fitness-gym --timeout=180s
                    kubectl get deployment,service aceest-fitness-gym
                '''
            }
        }
    }
}

