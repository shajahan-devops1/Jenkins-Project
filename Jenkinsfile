
pipeline {

    agent {
        label 'Node-1'
    }

    environment {
        VERSION = ''
    }

    stages {

        stage('Read Version') {
            steps {
                script {
                    def pkg = readJSON file: 'package.json'

                    env.VERSION = pkg.version

                    echo "Package version: ${env.VERSION}"
                }
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Docker build') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-creds']
                ]) {
                    sh '''
                        aws sts get-caller-identity

                        aws ecr get-login-password \
                          --region us-east-1 | \
                          docker login \
                          --username AWS \
                          --password-stdin \
                          637423198678.dkr.ecr.us-east-1.amazonaws.com

                        docker build \
                          -t 637423198678.dkr.ecr.us-east-1.amazonaws.com/roboshop/catalogue:${VERSION} .

                        docker push \
                          637423198678.dkr.ecr.us-east-1.amazonaws.com/roboshop/catalogue:${VERSION}
                    '''
                }
            }
        }

    }
}