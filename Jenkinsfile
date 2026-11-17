
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                echo 'GitHub code downloaded'
            }
        }

        stage('Build') {
            steps {
                echo 'Preparing website'
                sh 'ls -la'
                sh 'test -f index.html'
            }
        }

        stage('Deploy') {
            steps {
                sshagent(credentials: ['deployment-ssh']) {
                    sh '''
                        scp -o StrictHostKeyChecking=yes \
                        index.html ubuntu@172.31.3.30:/var/www/html/index.html
                    '''
                }
            }
        }

        stage('Verify') {
            steps {
                sshagent(credentials: ['deployment-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=yes \
                        ubuntu@172.31.3.30 \
                        "curl -f http://localhost/"
                    '''
                }
            }
        }
    }
}
