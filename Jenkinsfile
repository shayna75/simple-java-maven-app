pipeline{
  agent any
  tools {
        maven 'maven3.3.9' 
    }
  environment{
	    NEXUS_VERSION = "nexus3"
	    NEXUS_PROTOCOL = "http"
	    NEXUS_URL = "127.0.0.1:8881"
	    NEXUS_REPOSITORY = "sample-maven-repo"
	   NEXUS_CREDENTIALS_ID = "nexus-credentials"
  }
  stages{
    stage('Build'){
      steps{
        sh 'mvn -B -DskipTests clean package'
      }
    }
    stage('Test'){
      steps{
        sh 'mvn test'
      }
      post{
        always{
          junit 'target/surefire-reports/*.xml'
        }
      }
    }
    stage("publish to nexus"){
      steps{
        script{
          pom = readMavenPom file: "pom.xml";
          filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
          echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
          artifactPath = filesByGlob[0].path;
          artifactExists = fileExists artifactPath;

          if(artifactExists){
            echo "***File: ${artifactPath}, group: ${pom.groupId},artifactName: ${pom.artifactId} ,packaging: ${pom.packaging}, version ${pom.version} ***"
            nexusArtifactUploader(
              nexusVersion: NEXUS_VERSION,
              protocol: NEXUS_PROTOCOL,
              nexusUrl: NEXUS_URL,
              groupId: pom.groupId,
              version: pom.version,
              repository: NEXUS_REPOSITORY,
              credentialsId: NEXUS_CREDENTIALS_ID,
              artifacts: [
              [artifactId: pom.artifactId, classifier: '', file: artifactPath, type: pom.packaging],
              [artifactId: pom.artifactId, classifier: '', file: "pom.xml", type: "pom"]
              ]
            );
          }
          else{
            error "*** File ${artifactPath}, could not be found  ***"
          }

        }
      }
    }
  }
}
