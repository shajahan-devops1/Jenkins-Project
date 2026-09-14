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
                withSonarQubeEnv('sonar-scanner') {         #withSonarQubeEnv() should have the same name as the SonarQube server configured in Jenkins under system configuration
                    sh "${tool 'sonar-8'}/bin/sonar-scanner"        #name should match the name mentioned in tools configuration in Jenkins
                }
            }
        }
        stage('Docker build') {
            steps {
                script {
                    withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                        sh """
                            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 906303433456.dkr.ecr.us-east-1.amazonaws.com
                            docker build -t 906303433456.dkr.ecr.us-east-1.amazonaws.com/roboshop/catalogue:${version} .
                            docker push 906303433456.dkr.ecr.us-east-1.amazonaws.com/roboshop/catalogue:${version}
                        """
                    }
                }
            }
        }

    }
}