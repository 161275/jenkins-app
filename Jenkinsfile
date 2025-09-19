pipeline {
    agent any
    
    environment {
        // NETLIFY_SITE_ID = '6e92f3a3-0f41-4fd1-b86f-4a04c91b8aba'
        // NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        AWS_S3_BUCKET = 'jenkins-app-nish'
        AWS_DEFAULT_REGION = 'us-east-1'
    }

    stages {
        stage('AWS'){
            agent {
                docker {
                    image 'amazon/aws-cli'
                    args "--entrypoint=''"
                    reuseNode true
                }
            }
            steps{
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                    aws --version
                    # aws s3 ls
                    # aws s3 sync build s3://$AWS_S3_BUCKET/
                    aws ecs register-task-definition --cli-input-json file://aws/task-def.json --region $AWS_DEFAULT_REGION
                    aws ecs update-service --cluster jenkins-app-prod --service jenkins-app-taskdef-prod-service --task-definition jenkins-app-taskdef-prod:2
                    '''
                }
                
            }
        }
        stage('build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm ci 
                npm run build
                '''
            }
        }
        // stage('TEST') {
        //     parallel {
        //         stage('unit test') {
        //             agent {
        //                 docker {
        //                     image 'node:18-alpine'
        //                     reuseNode true
        //                 }
        //             }
        //             steps {
        //                 sh '''
        //                 npm test
        //                 '''
        //             }
        //             post {
        //                 always {
        //                     junit 'jest-results/junit.xml'
        //                 }
        //             }
        //         }
        //         stage('e2e') {
        //             agent {
        //                 docker {
        //                     image 'my-playwright'
        //                     reuseNode true
        //                 }
        //             }
        //             steps {
        //                 sh '''
        //                 # npm install serve
        //                 # node_modules/.bin/serve -s build &
        //                 serve -s build &
        //                 sleep 10
        //                 npx playwright test --reporter=html
        //                 '''
        //             }
        //             post {
        //                 always {
        //                     publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'playwright Local HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        //                 }
        //             }
        //         }
        //     }
        // }

        // stage('deploy staging') {
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //         npm install netlify-cli@20.1.1 node-jq
        //         node_modules/.bin/netlify --version
        //         node_modules/.bin/netlify status
        //         node_modules/.bin/netlify deploy --dir=build --json > stage_data.json
        //         '''
        //         script {
        //             env.STAGE_URL = sh(script: 'node_modules/.bin/node-jq -r ".deploy_url" stage_data.json', returnStdout: true)
        //         }
        //     }
        // }

        // stage('Stage') {
        //     agent {
        //         docker {
        //             image 'my-playwright'
        //             reuseNode true
        //         }
        //     }
        //     environment {
        //         //CI_ENVIRONMENT_URL = 'https://dulcet-cheesecake-60d93a.netlify.app'
        //         CI_ENVIRONMENT_URL = 'need-to-be-set'
        //     }
        //     steps {
        //         sh '''
        //         # npm install netlify-cli@20.1.1 node-jq
        //         # node_modules/.bin/netlify --version
        //         # node_modules/.bin/netlify status
        //         # node_modules/.bin/netlify deploy --dir=build --json > stage_data.json
        //         netlify --version
        //         netlify status
        //         netlify deploy --dir=build --json > stage_data.json
        //         sleep 5
        //         CI_ENVIRONMENT_URL=$(jq -r ".deploy_url" stage_data.json)
        //         npx playwright test --reporter=html
        //         '''
        //     }
        //     post {
        //         always {
        //             publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'playwright stage HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        //         }
        //     }
        // }
        
        // stage('Approval') {
        //     steps {
        //         timeout(time: 15, unit: 'HOURS') {
        //         input message: 'Do you wish to deploy to production?', ok: 'Yes, I am sure!'
        //     }
        //     }
        // }
        // stage('deploy prod') {
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //         #npm install netlify-cli@20.1.1
        //         node_modules/.bin/netlify --version
        //         node_modules/.bin/netlify status
        //         node_modules/.bin/netlify deploy --dir=build --prod
        //         '''
        //     }
        // }
        
        // stage('Prod') {
        //     agent {
        //         docker {
        //             image 'my-playwright'
        //             reuseNode true
        //         }
        //     }
        //     environment {
        //         CI_ENVIRONMENT_URL = 'https://dulcet-cheesecake-60d93a.netlify.app'
        //     }
        //     steps {
        //         sh '''
        //         #npm install netlify-cli@20.1.1
        //         netlify --version
        //         netlify status
        //         netlify deploy --dir=build --prod
        //         npx playwright test --reporter=html
        //         '''
        //     }
        //     post {
        //         always {
        //             publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'playwright prod HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        //         }
        //     }
        // }
       
    }
    
}