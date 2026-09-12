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

    }
}