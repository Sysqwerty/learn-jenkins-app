pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh """
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                """
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh """
                    test -f build/index.html
                    npm test
                    test -f test-results/junit.xml
                """
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.51.0-noble'
                    reuseNode true
                }
            }
            steps {
                sh """
                    npm install -g serve
                    serve -s build
                    npx playwright test
                """
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
