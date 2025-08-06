pipeline {
    agent any

    stages {
        stage('build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-u root:root'
                    reuseNode true
                }
            }
            steps {
                sh '''
                echo 'Hello, World!'
                npm ci
                npm run build
                ls -la
                '''
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-u root:root'
                    reuseNode true
                }
            }
            steps {
                sh '''
                echo "Test stage"
                test -f build/index.html
                npm test
                ls -la
                '''
            }
        }
        stage('PlaywrightTest') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.54.0-noble'
                    args '-u root:root'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm install serve
                serve -s build &
                sleep 10
                npx playwright test
                '''
            }
        }
    }
    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}
