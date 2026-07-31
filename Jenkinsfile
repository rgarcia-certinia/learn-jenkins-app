pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image "node:18-alpine"
                    reuseNode true
                }
            }

            steps {
                sh '''
                # this is a comment 
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Run Tests') {
            parallel {
                stage ('Unit') {
                    agent {
                        docker {
                            image "node:18-alpine"
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            npm test
                        '''
                    }

                    post {
                        always {
                            junit 'playwright-results/junit.xml'
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }

                stage('E2E') {
                    agent {
                        docker {
                            image "mcr.microsoft.com/playwright:v1.39.0-focal"
                            reuseNode true
                        }
                    }
            
                    steps {
                        sh '''
                            npm i serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }

                    post {
                        always {
                            junit 'playwright-results/junit.xml'
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }
    }
}
