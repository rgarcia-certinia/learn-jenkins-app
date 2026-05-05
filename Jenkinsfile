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

        stage('Test') {
            agent {
                docker {
                    image "node:18-alpine"
                reuseNode true
                }
            }
            
            steps {
                sh '''
                test -f build/index.html
                CI=true npm test
                '''
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
