pipeline {

    agent {
        label 'Node-1'
    }

    environment {
        def version = ""
    }

    stages {

        stage('Read Version') {
            steps {

                script {
                    // Read package.json as a Map
                    def pkg = readJSON file: 'package.json'

                    // Extract version
                    version = pkg.version

                    // Print version
                    echo "Package version: ${version}"
                }

            }
        }

        stage('Build') {
            steps {
                sh "npm install"
            }
        }

        stage('SonarQube Analysis') {
            steps {
            withSonarQubeEnv('sonar-scan') {
                sh "${tool 'sonarscan-8'}/bin/sonar-scanner"
            }
        }
        stage('Docker build') {
            steps {
                script {
                    withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                        sh """
                            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 533267079470.dkr.ecr.us-east-1.amazonaws.com
                            docker build -t 533267079470.dkr.ecr.us-east-1.amazonaws.com/roboshop/catalogue:${version} .
                            docker push 533267079470.dkr.ecr.us-east-1.amazonaws.com/roboshop/catalogue:${version}
                        """
                    }
                }
            }
        }

    }
}