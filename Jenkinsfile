pipeline {
    agent any
    options {
        skipDefaultCheckout (true)
    }
    stages {
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

        stage('Test') {
            agent {
                docker {
                    image 'node:22-alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    npm run test
                '''
            }
        }
    }
}