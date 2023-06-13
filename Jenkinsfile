pipeline{

agent any

tools{
maven "maven"

}

	environment {
	credentials = 'ecr:us-east-1:aws-credentials'
	url = "https://327575778641.dkr.ecr.us-east-1.amazonaws.com"
	imagename = "327575778641.dkr.ecr.us-east-1.amazonaws.com/uday-ecr-repo"
	cluster = "udayecs"
        service = "udayservice"
    }
/*
triggers{
pollSCM('* * * * *')
}

options{
timestamps()
buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5'))
}
*/
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
   sh "scp -o StrictHostKeyChecking=no target/studentapp-2.5-SNAPSHOT.war ec2-user@3.91.59.202:/opt/tomcat/webapps/"    
  }
  }
  }
  
/*
 stage('Build Docker Image') {
  steps {
    sh 'docker build -t imagename .'
  }
}

 stage('Run Docker Container') {
  steps {
    sh 'docker run -itd -p 89:8080 --name cont2 image2'
  }
}
*/

	 stage('build docker image') {
	       steps {
	         script  {
	            dockerImage =docker.build(imagename)
	         }
	       }
	 }
	  
	    stage('upload to ecr') {
	    steps {
	        script {
	          docker.withRegistry(url,credentials) {   
	          dockerImage.push("$BUILD_NUMBER")
	          dockerImage.push('latest') }
	        
	        }

	    }
	  }
	  stage('Deploy to ecs') {
          steps {
          withAWS(credentials: 'aws-credentials', region: 'us-east-1') {
          sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
        }
      }
     }
 
}
				
}
