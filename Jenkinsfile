pipeline {
    agent any

    environment {
    NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    NETLIFY_SITE_ID = 'b060b76d-4b40-48d3-9940-07357592f5e3'
  }

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
                npm install netlify-cli@latest -g
                netlify --version
                netlify status
                netlify deploy --dir=build --prod
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
