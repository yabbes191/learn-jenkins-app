pipeline {
    agent any 
    environment{
        NETLIFY_SITE_ID ='cf33c88b-959d-4b5c-af29-4f8e8dd59231'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }
    stages{
        // stage('Build'){
        //     agent {
        //         docker{
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps{
        //         sh ''' 
        //             ls -la
        //             node --version
        //             npm --version
        //             npm ci
        //             npm run build
        //             ls -la
        //          '''
        //     }
        // }
        
        stage('Test'){
            parallel{
                stage('Unit Test'){
                    agent {
                        docker{
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps{
                        sh '''
                            echo "Test stage"
                            test -f build/index.html
                            npm test
                        '''
                    }
                }
                
                 stage('E2E'){
                    agent {
                        docker{
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps{
                        sh '''
                        npm install serve
                        node_modules/.bin/serve -s build &
                        #sleep 10
                        #npx playwright test --reporter=html
                        '''
                    }
                }
            }
           
        }

         stage('Deploy'){
            agent {
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh ''' 
                   npm install netlify-cli
                   node_modules/.bin/netlify --version
                   echo "Deploy to production site id : $NETLIFY_SITE_ID"
                   node_modules/.bin/netlify status
                 '''
            }
        }
        
      
    }
      post{
            always{
                junit 'jest-results/junit.xml'
            }
        }
}