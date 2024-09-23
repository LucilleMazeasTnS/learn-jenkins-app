pipeline {
    agent any
    
    stages{
        agent{
                docker{
                    image "node:18-alpine"
                    reuseNode true
                }
        }
        stage('Build'){
            
            steps{
                echo "Building the software ..."
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        stage('Test'){
            steps{
                echo "Testing the software ..."
                sh "test -f build/index.html"
                sh "npm test"
            }
        }
    }
}