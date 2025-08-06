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
                mkdir -p ~/.npm
                chown -R $(id -u):$(id -g) ~/.npm
                npm ci
                npm run build
                ls -la
'''
            }
        }
    }
}
