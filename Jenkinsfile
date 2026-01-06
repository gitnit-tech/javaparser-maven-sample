pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {

        stage('Build') {
            steps {
                echo "--------- build started"
                sh 'mvn clean package -Dmaven.test.skip=true'
                echo "--------- build completed"
            }
        }

        stage('Test') {
            steps {
                echo "--------- unit test started"
                sh 'mvn surefire-report:report'
                echo "--------- unit test completed"
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
                        -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to Quality Gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
                          
