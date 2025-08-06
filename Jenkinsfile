pipeline {
    agent any

    stages {
        stage('build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args ''  // Use non-root user
                    reuseNode true
                }
            }
            steps {
                sh '''
                echo 'Hello, World!'
                echo "User: $(whoami)"
                echo "HOME is $HOME"
                mkdir -p /home/node/.npm


                npm ci
                npm run build

                ls -la
                '''
            }
        }
    }
}
