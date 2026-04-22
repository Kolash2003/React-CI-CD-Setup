pipeline {
    agent any

    environment {
        NODE_ENV = 'test'
        VERCEL_TOKEN = credentials('VERCEL_TOKEN')
    }

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

        stage('Deploy') {
            agent {
                docker {
                    image 'node:22-alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    export HOME="${WORKSPACE}/.fake_home"
                    mkdir -p "${HOME}"
                    export npm_config_cache="${WORKSPACE}/.npm-cache"
                    export npm_config_prefix="${WORKSPACE}/.npm-global"
                    export PATH="${WORKSPACE}/.npm-global/bin:${PATH}"
                    npm install -g vercel
                    vercel --prod --token=$VERCEL_TOKEN --yes --name=react-ci-cd-project
                '''
            }
        }
    }
}