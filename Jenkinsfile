pipeline {
    agent any  // instead of agent { docker { ... label 'docker' } }


   environment {
    NETLIFY_AUTH_TOKEN = credentials('NETLIFY_AUTH_TOKEN') // this ID points to Jenkins credential
    NETLIFY_SITE_ID = '4f68f710-1352-4cb0-a12d-690d6731b585'               // this is public
}

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                    echo "Node version:"
                    node --version
                    echo "NPM version:"
                    npm --version

                    echo "Installing packages..."
                    npm install

                    echo "Building app..."
                    npm run build

                    echo "Contents of build folder:"
                    ls -la build
                '''
            }
        }
        
        stage('Test') {
            steps {
                sh '''
                echo "Running tests..."
                npm test -- --watchAll=false
                '''   
            }
        }

         stage('Deploy to Netlify') {
            steps {
                // Use withCredentials for extra safety
                withCredentials([string(credentialsId: 'NETLIFY_AUTH_TOKEN', variable: 'NETLIFY_AUTH_TOKEN')]) {
                    sh '''
                        echo "Deploying to Netlify..."
                        npx netlify deploy --prod --dir=build --auth=$NETLIFY_AUTH_TOKEN --site=$NETLIFY_SITE_ID
                    '''
                }
            }
        }
    }
}
