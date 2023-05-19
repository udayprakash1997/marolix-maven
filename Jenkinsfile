pipeline{

agent any

tools{
maven "maven"

}

triggers{
pollSCM('* * * * *')
}

options{
timestamps()
buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5'))
}

stages{

  stage('CheckOutCode'){
    steps{
    git 'https://github.com/udayprakash1997/marolix-maven.git'
	}
  }
  
  stage('Build'){
  steps{
  sh  "mvn clean package"
  }
  }
  stage('ExecuteSonarQubeReport'){
  steps{
  sh  "mvn clean package sonar:sonar"
  }
  }

  stage('UploadArtifactsIntoNexus'){
  steps{
  sh  "mvn clean deploy"
  }
  }
  stage('DeployAppIntoTomcat'){
  steps{
  sshagent(['f69ee4f7-2422-4062-bff8-0f6445bdbf1f']) {
   sh "scp -o StrictHostKeyChecking=no target/studentapp-2.5-SNAPSHOT.war ec2-user@107.23.38.38:/opt/tomcat/webapps/"    
  }
  }
  }
}
}
