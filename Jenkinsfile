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

        stage('Trivy Dockerfile Scan') {
            steps {
                sh """
                    trivy config --exit-code 1 --severity HIGH,CRITICAL --format table .
                """
            }
        }

        // stage('SonarQube Analysis') {
        //     steps {
        //         withSonarQubeEnv('sonar-scanner') {
        //             sh "${tool 'sonar-8'}/bin/sonar-scanner"
        //         }
        //     }
        // }
        stage('Docker build') {
            steps {
                script {
                    withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                        sh """
                            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 906303433456.dkr.ecr.us-east-1.amazonaws.com
                            docker build -t 906303433456.dkr.ecr.us-east-1.amazonaws.com/roboshop/catalogue:${version} .
                        """
                    }
                }
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh """
                    trivy image --quiet --cache-dir /home/ec2-user/.trivy-cache --scanners vuln --pkg-types os --exit-code 1 --severity HIGH,CRITICAL --format table 160885265516.dkr.ecr.us-east-1.amazonaws.com/roboshop/catalogue:${version}
                """
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                        sh """
                            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 906303433456.dkr.ecr.us-east-1.amazonaws.com
                            docker push 906303433456.dkr.ecr.us-east-1.amazonaws.com/roboshop/catalogue:${version}
                        """
                    }
                }
            }

        }

    }
}