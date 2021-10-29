def secrets=[
      [path: 'secret/alpine/dev/artifactory', engineVersion: 1, secretValues: [
          [envVar: 'artifactory_token', vaultKey: 'swift-artifactory-token']]],
 ]  
def configuration = [vaultUrl: 'https://vault.kube.cxmgmtawsprd.massmutual.com',  vaultCredentialId: 'vault', engineVersion: 1]
pipeline
{
    agent any
    // Define environment variables which can be used in the pipeline at any stage
    environment {
        IMAGE_NAME = "test-docker"
        IMAGE_TAG = 1.0
        
        //Use Jfrog Artifactory option to store docker images
        JFROG_ARTIFACTORY = 'artifactory.awsmgmt.massmutual.com/daas-swift-docker'   
        SWIFT_ARTIFACTORY_TOKEN = credentials('fim-artifactory-daas-swift-rw-token')
        SWIFT_ARTIFACTORY_USER = "fim-artifactory-daas-swift-rw-token"
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

       // stage("Push to Artifactory") {
       //     steps {
       //        withVault([configuration: configuration, vaultSecrets: secrets]) {
       //         sh "echo ${env.artifactory_token}"
       //         sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${JFROG_ARTIFACTORY}/${IMAGE_NAME}:${IMAGE_TAG}"
       //         echo "push image to Jfrog Artifactory"                    
       //         sh "docker login artifactory.awsmgmt.massmutual.com -u fim-artifactory-daas-swift-rw-token -p ${artifactory_token}"
       //         echo "Pushing imaging to Artifactory"
       //         sh "docker push $JFROG_ARTIFACTORY/$IMAGE_NAME:$IMAGE_TAG"
       //         
       //     }
       // }
       // }
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
