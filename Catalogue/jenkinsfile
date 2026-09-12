pipeline {

    agent {
        label 'Node-1'
    }

    stages {

        stage('Read Version') {
            steps {

                script {
                    // Read package.json as a Map
                    def pkg = readJSON file: 'package.json'

                    // Extract version
                    def version = pkg.version

                    // Print version
                    echo "Package version: ${version}"
                }

            }
        }

    }
}