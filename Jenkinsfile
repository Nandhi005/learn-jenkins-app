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
                echo "HOME is $HOME"
                mkdir -p $HOME/.npm
                chown -R $(id -u):$(id -g) $HOME/.npm
                npm ci
                npm run build
                ls -la
'''
            }
        }
    }
}
