pipeline {
    agent any

    stages {
        
        stage('build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                echo 'Hello, World!'
                ls -la
                npm --version
                mkdir -p /.npm && sudo chown -R $(id -u):$(id -g) /.npm
                npm ci
                npm run build
                ls -la
'''
            }
        }
    }
}
