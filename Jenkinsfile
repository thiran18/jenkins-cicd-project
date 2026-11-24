
pipeline {

    agent any

    options {
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Downloading code from GitHub'

                checkout scm
            }
        }

        stage('Validate') {
            steps {
                echo 'Checking website files'

                sh '''
                    if [ ! -f index.html ]; then
                        echo "index.html not found"
                        exit 1
                    fi

                    echo "Website files verified"
                '''
            }
        }

        stage('Deploy') {
            steps {

                echo 'Deploying to deployment server'

                sshagent(credentials: ['deployment-ssh']) {

                    sh '''
                        scp -o StrictHostKeyChecking=no \
                            index.html \
                            deploy@172.31.0.32:/var/www/html/index.html

                        ssh -o StrictHostKeyChecking=no \
                            deploy@172.31.0.32 \
                            "chmod 644 /var/www/html/index.html"
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {

                echo 'Verifying website'

                sshagent(credentials: ['deployment-ssh']) {

                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                            deploy@172.31.0.32 \
                            "curl -f http://localhost/"
                    '''
                }
            }
        }
    }

    post {

        success {
            echo 'SUCCESS: Website deployed!'
        }

        failure {
            echo 'FAILED: Check Jenkins Console Output'
        }
    }
}