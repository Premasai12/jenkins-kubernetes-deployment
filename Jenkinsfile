pipeline {

  agent any

  stages {

    stage('Code Checkout')
		{
			steps
			{
				git 'https://github.com/Premasai12/javaspringboot1.git'
			}
		}

    stage('Build Docker Image')
 		{
			steps
 			{
				sh 'cd /var/lib/jenkins/workspace/$JOB_NAME/'
 
				sh 'cp /var/lib/jenkins/workspace/$JOB_NAME/target/addressbook.war /var/lib/jenkins/workspace/$JOB_NAME/'
 
				sh 'docker build -t addressbook:$BUILD_NUMBER .'
 
				sh 'docker tag addressbook:$BUILD_NUMBER psr329/addressbook:$BUILD_NUMBER'
 			}
		}
		stage('Push Docker Image')
 		{
			steps
 			{
				withDockerRegistry([ credentialsId: "dockerhub", url: "" ])
 				{
					sh 'docker push psr329/addressbook:$BUILD_NUMBER'
 				}
			}
 		}
  		stage('Deploy as container')
 		{
			steps
 			{
				sh 'docker run -itd -P psr329/addressbook:$BUILD_NUMBER'
			}
		}

    stage('PushToArtifactory')
        {
            steps 
            {
                dir("/var/lib/jenkins/workspace/artifactory/targets")
                {
                script 
                {
                    sh 'ls'
                    server= Artifactory.server "${artifactoryServiceID}"
                    def uploadSpec = """{
                    "files":[
                    {
                        "pattern": "target/*.jar",
                        "target": "vzIoT/"
                    }]
                    }"""
                    server.upload uploadSpec
                }
                }
            }    
        }
    stage('Deploying App to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deployment.yaml", "service.yaml")
        }
      }
    }

  }

}
