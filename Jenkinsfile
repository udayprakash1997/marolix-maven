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
/*
  stage('ExecuteSonarQubeReport'){
  steps{
  sh  "mvn clean sonar:sonar"
  }
  }
*/
  stage('UploadArtifactsIntoNexus'){
  steps{
  sh  "mvn clean deploy"
  }
  }
  stage('DeployAppIntoTomcat'){
  steps{
  sshagent(['8db8284f-c2f9-454b-a923-da710f351ec5']) {
   sh "scp -o StrictHostKeyChecking=no target/studentapp-2.5-SNAPSHOT.war ubuntu@3.83.176.122:/root/tomcat/webapps/"    
  }
  }
  }
}
}
