pipeline{
  agent any
  tools {
        maven 'maven3.3.9' 
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
    }
  }
}
