pipeline {
    agent any
    
    environment{
        NETLIFY_SITE_ID '4d7b5d43-f59b-4d10-8058-ec9c974c529a'
    }
    stages{
        stage('Build'){
            agent {
                docker{
                    image "node:18-alpine"
                    reuseNode true
                }
            }
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
        stage('Run Tests'){
            parallel{
                stage('Unit Test'){
                    agent {
                        docker{
                            image "node:18-alpine"
                            reuseNode true
                        }
                    }
                    steps{
                        echo "Testing the software ..."
                        sh "test -f build/index.html"
                        sh "npm test"
                    }
                    post{
                        always{
                            junit 'jest-results/junit.xml'
                        }
                    }
                }
                stage('E2E'){
                    agent {
                        docker{
                            image "mcr.microsoft.com/playwright:v1.39.0-jammy"
                            reuseNode true
                        }
                    }
                    steps{
                        sh '''
                        npm install serve
                        node_modules/.bin/serve -s build &
                        sleep 10
                        npx playwright test --reporter=html
                        '''
                    }
                    post{
                        always{
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }

        stage('Deploy'){
            agent {
                docker{
                    image "node:18-alpine"
                    reuseNode true
                }
            }
            steps{
                echo "Building the software ..."
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying $NETLIFY_SITE_ID"
                '''
            }
        }
    }
}