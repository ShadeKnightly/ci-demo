pipeline {
    agent any  

    environment {
        NETLIFY_AUTH_TOKEN = credentials('NETLIFY_AUTH_TOKEN')
        NETLIFY_SITE_ID = '4f68f710-1352-4cb0-a12d-690d6731b585'
    }

    stages {
        stage('Docker') {
            steps {
                sh 'docker build -t my-docker-image .'
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:24.14.0-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm install
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:24.14.0-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    test -f build/index.html
                    npm test -- --watchAll=false
                '''
            }
        }

        stage('AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args '--entrypoint=""'
                }
            }
            environment {
                AWS_S3_BUCKET = 'shade-knightly-react-app-2026'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'tempAWS', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version
                        aws s3 ls
                        aws s3 sync build s3://$AWS_S3_BUCKET
                    '''
                }
            }
        }
    }
}