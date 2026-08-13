pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                sh '''
                    python3 -m venv venv
                    source venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    pip install pytest
                    pytest -v
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build -t ci-cd-demo:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker stop ci-cd-demo || true
                    docker rm ci-cd-demo || true

                    docker run -d \
                      --name ci-cd-demo \
                      -p 8081:5000 \
                      ci-cd-demo:${BUILD_NUMBER}
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    sleep 5
                    curl --fail http://localhost:8081/health
                '''
            }
        }
    }
}
