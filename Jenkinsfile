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
                        ${scannerHome}/bin/sonar-scanner
                    """
                }
            }
        }

        // quality gate test Skipped
              
        

        stage('Jar Publish') {
            steps {
                script {

                    echo "------------- Jar Publish Started -------------"

                    def server = Artifactory.newServer(
                        url: 'http://13.205.1.93:8081/artifactory',
                        credentialsId: 'Artifact-Credential'
                    )

                    def properties = "buildid=${env.BUILD_ID};commitid=${env.GIT_COMMIT}"

                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstaging/(*)",
                                "target": "libs-release-local/{1}",
                                "flat": "false",
                                "props": "${properties}",
                                "exclusions": ["*.sha1", "*.md5"]
                            }
                        ]
                    }"""

                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)

                    echo "------------- Jar Publish Ended ---------------"
                }
            }
        }
    }
}

