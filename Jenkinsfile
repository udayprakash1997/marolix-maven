pipeline{

agent any

tools{
maven "maven"

}
  environment {
        DOCKER_IMAGE_NAME = 'uday-ecr-repo:latest'  // Specify the name and tag of your Docker image
        ECR_REPOSITORY = 'uday-ecr-repo'  // Specify the name of your ECR repository
        AWS_REGION = 'us-east-1'  // Specify the AWS region where your ECR repository is located
       // AWS_CREDENTIALS_ID = 'aws-credentials'  // Specify the ID of your AWS credentials stored in Jenkins
	AWS_ACCOUNT_ID = '327575778641'
	AWS_ACCESS_KEY_ID = ''
        AWS_SECRET_ACCESS_KEY = ''
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
   sh "scp -o StrictHostKeyChecking=no target/studentapp-2.5-SNAPSHOT.war ec2-user@54.87.144.170:/opt/tomcat/webapps/"    
  }
  }
  }
/*
 stage('Build Docker Image') {
  steps {
    sh 'docker build -t image1 .'
  }
}
 stage('Run Docker Container') {
  steps {
    sh 'docker run -itd -p 89:8080 --name cont1 image1'
  }
}
 */
stage('Build and Push Docker Image') {
          steps {
                //withAWS(credentials: awsCredentials(AWS_CREDENTIALS_ID), region: AWS_REGION) {
                    withCredentials([
                    string(credentialsId: 'aws-credentials', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-credentials', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) 
		  script {
                         // Assign the values to environment variables
                        env.AWS_ACCESS_KEY_ID = sh(script: 'echo $AWS_ACCESS_KEY_ID', returnStdout: true).trim()
                        env.AWS_SECRET_ACCESS_KEY = sh(script: 'echo $AWS_SECRET_ACCESS_KEY', returnStdout: true).trim()
                        
			  // Build the Docker image
                        sh "docker build -t ${DOCKER_IMAGE_NAME} ."
                        
                        // Authenticate with ECR
                        sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
                        
                        // Tag the Docker image with ECR repository URI
                        sh "docker tag ${DOCKER_IMAGE_NAME} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${BUILD_NUMBER}"
                        
                        // Push the Docker image to ECR
                        sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${BUILD_NUMBER}"
                    }
                }
            }
        }
}
