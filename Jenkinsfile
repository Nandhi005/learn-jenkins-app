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
            image 'mcr.microsoft.com/playwright:v1.39.0'
            args '-u root:root'
            reuseNode true
        }
    }
    steps {
        sh '''
        npx serve -s build &
        sleep 10
        npx playwright test
        '''
    }
}
        stage('deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-u root:root'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm install netlify-cli@20.1.1
                netlify --version
                '''
            }
        }
}
    post {
        always {
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}
