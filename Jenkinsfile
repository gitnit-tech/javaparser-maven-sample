pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {

        stage('build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'My-SonarQube-Scanner'
            }

            steps {
                withSonarQubeEnv('My-SonarQube-Server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
    }
}
