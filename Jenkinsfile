pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {

        stage('Build') {
            steps {
                echo "--------- build started ----------"
                sh 'mvn clean package -Dmaven.test.skip=true'
                echo "--------- build completed --------"
            }
        }

        stage('Test') {
            steps {
                echo "--------- unit test started ------"
                sh 'mvn test'
                echo "--------- unit test completed ----"
            }
        }

        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'My-SonarQube-Scanner'
            }
            steps {
                withSonarQubeEnv('My-SonarQube-Server') {
                    sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=my-java-project \
                        -Dsonar.projectName=my-java-project \
                        -Dsonar.sources=src/main/java \
                        -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }
    }
}
                      
