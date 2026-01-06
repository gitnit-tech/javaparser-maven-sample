stage('Jar Publish') {
    steps {
        script {

            echo "------------- Jar Publish Started -------------"

            // ✅ STEP 1: Prepare jarstaging directory
            sh '''
                mkdir -p jarstaging
                cp target/*.jar jarstaging/
                echo "JARs available for upload:"
                ls -l jarstaging
            '''

            def server = Artifactory.newServer(
                url: 'http://13.205.1.93:8081/artifactory',
                credentialsId: 'Artifact-Credential'
            )

            def properties = "buildid=${env.BUILD_ID};commitid=${env.GIT_COMMIT}"

            def uploadSpec = """{
                "files": [
                    {
                        "pattern": "jarstaging/(*)",
                        "target": "nitish/{1}",
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



