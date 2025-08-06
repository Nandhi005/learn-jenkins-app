pipeline {
    agent any

    stages {
        stage('build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-u node -w /tmp'
                    reuseNode true
                }
            }
            environment {
                npm_config_cache = '/tmp/.npm'
            }
            steps {
                sh '''
                echo 'Hello, World!'
                echo "User: $(whoami)"
                echo "HOME is $HOME"

                mkdir -p $npm_config_cache

                npm ci
                npm run build

                ls -la
                '''
            }
        }
    }
}
