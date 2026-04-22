pipeline {
    agent any
    stages {
        options {
            skipDefaultCheckout (true)
        }
        stage('Clean up code') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout using SCM') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:22-alpine'
                    reuseNode true  // Reuse the node for the next stages
                }
            }

            steps {
                sh '''
                    export npm_config_cache="${WORKSPACE}/.npm-cache"
                    ls -l
                    node --version
                    npm --version
                    npm install
                    npm run build
                    ls -l
                '''
            }
        }
    }
}