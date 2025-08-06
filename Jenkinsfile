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
                npm install
                npm run build
                ls -la
'''
            }
        }
    }
}
