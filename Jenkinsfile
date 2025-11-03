pipeline {
    agent any
    
    environment {
        DOCKER_HOST = "unix:///var/run/docker.sock"
    }
    
    stages {
        stage('Verify Tools') {
            steps {
                sh '''
                    echo "=== Verificando herramientas ==="
                    docker --version
                    docker-compose --version
                    echo "=== Estructura del proyecto ==="
                    pwd
                    ls -la
                '''
            }
        }
        
        stage('Build') {
            steps {
                sh 'docker-compose build'
            }
        }
        
        stage('Test') {
            steps {
                sh 'docker-compose -f docker-compose.test.yml up --abort-on-container-exit --exit-code-from test-web'
            }
            post {
                always {
                    sh 'docker-compose -f docker-compose.test.yml down'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sh '''
                    docker-compose down || true
                    docker-compose up -d
                    sleep 15
                '''
            }
        }
        
        stage('Smoke Test') {
            steps {
                sh '''
                    curl -f http://localhost:5000/login && \
                    echo "✅ Smoke test pasado" || \
                    (echo "❌ Smoke test falló" && exit 1)
                '''
            }
        }
    }
    
    post {
        always {
            sh 'docker-compose down || true'
            cleanWs()
        }
    }
}
