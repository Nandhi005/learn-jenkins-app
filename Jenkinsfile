pipeline {
    agent any

    environment {
    NETLIFY_SITE_ID = 'b060b76d-4b40-48d3-9940-07357592f5e3'
    NETLIFY_AUTH_TOKEN = credentials('netlify-token')
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
        stage('AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    args "--entrypoint=''"
                    reuseNode true
              }
}
            environment {
                AWS_BUCKET_NAME= 'my-aws-11082025'
            }
            steps {
                
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                  sh''' 
                   aws --version
                   echo "hello Nandhish" > index.html
                   aws cp index.html S3://$AWS_BUCKET_NAME/index.html
                   '''
                }
        
            }
        }
        stage('TEST') {
            parallel{
                 stage('Unit Test') {
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
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
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
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
        }
            }
        }
        stage('Deploy Staging') {
            agent {
                docker {
                    image 'node:18'
                    args '-u root:root'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm install -g netlify-cli@20.0.2 node-jq
                npx netlify --version 
                npx netlify status
                npx netlify deploy --dir=build --json > deploy-output.json
                 '''
                script {
                    env.build_url = sh(script: "node-jq -r '.deploy_url' deploy-output.json", returnStdout: true)
                }
            }
            
        }
          stage('Deploy PlaywrightTest') {
             agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0'
                    args '-u root:root'
                    reuseNode true
                }
            }
            environment {
                CI_ENVIRONMENT_URL = "${env.build_url}"
            }
            steps {
                sh '''
                npx playwright test
                '''
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'staging Test HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
}
        stage('approval') {
            steps {
                timeout(2) {
                    input message: 'Do you wish to deploy to the production ?', ok: 'go Ahead!'
                }
            }
        }
        stage('deploy') {
            agent {
                docker {
                    image 'node:18'
                    args '-u root:root'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm install -g netlify-cli@20.0.2
                npx netlify --version 
                npx netlify status
                npx netlify deploy --dir=build --prod
                '''
            }
        }
          stage('Prod PlaywrightTest') {
             agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0'
                    args '-u root:root'
                    reuseNode true
                }
            }
            environment {
                CI_ENVIRONMENT_URL = 'https://stellar-sunburst-3f13e2.netlify.app'
            }
            steps {
                sh '''
                npx playwright test
                '''
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Prod HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
}
}
    
}
