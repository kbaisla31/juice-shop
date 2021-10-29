
pipeline
{
    agent any
    // Define environment variables which can be used in the pipeline at any stage
    environment {
        IMAGE_NAME = "test-docker"
        IMAGE_TAG = 1.0
       
    }
      
      tools {nodejs "nodejs16.5.0"}

    stages {
        stage("Snyk Scan") {
            steps {
                sh script: """
                 npm install snyk@latest -g
                 npm install snyk-to-html -g
                 snyk auth --API_TOKEN $SNYK_TOKEN
                 docker build -t $IMAGE_NAME:$IMAGE_TAG .
                 snyk container test $IMAGE_NAME:$IMAGE_TAG --json | snyk-to-html -o snykcontainerresults.html
                 snyk container monitor $IMAGE_NAME:$IMAGE_TAG
                """.stripIndent()
            }
        } 
        stage("cleanup") {
            steps {
            sh script: """
            echo "Removing docker image from local workspace"
            docker image rm $IMAGE_NAME:$IMAGE_TAG
            """.stripIndent()
            }
        }
    }
}
