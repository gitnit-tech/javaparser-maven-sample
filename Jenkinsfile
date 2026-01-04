pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {

        stage('build') {
            steps {
                echo "--------- build started"
                sh 'mvn clean package -Dmaven.test.skip=true'
                echo "--------- build completed"
            }
        }

        stage('test') {
            steps {
                echo "--------- unit test started"
                sh 'mvn surefire-report:report'
                echo "--------- unit test completed"
            }
        }

        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'My-SonarQube-Scanner'
            }
            steps {
                withSonarQubeEnv('My-SonarQube-Server') {
                    sh """
                    ${scannerHome}/bin/sonar-scanner \
                    -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 60, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}

