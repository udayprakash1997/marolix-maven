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
  sshagent(['8938384c-c728-48f8-845c-fbb69131a42e']) {
   sh "scp -o StrictHostKeyChecking=no target/* ec2-user@3.83.176.122:/opt/apache-tomcat-9.0.50/webapps/"    
  }
  }
  }
}
}
