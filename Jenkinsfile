node{
   stage('SCM Checkout'){
     git 'https://github.com/damodaranj/my-app.git'
   }
       stage('Compile-Package'){
      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis'){
  def mvnHome =  tool name: 'maven3', type: 'maven'   
	withsonarQubeEnv('sonar'){
	sh "${mvnHome}/bin/mvn sonar:sonar"
   } 
   }
   stage('Docker Image Build'){
   sh 'docker build -t priyait1995/myweb:0.0.2 .'  }
   stage('Push Docker Image'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u priyait1995 -p ${dockerPassword}"    }
   sh 'docker push priyait1995/myweb:0.0.2'
   } 
   
  stage('Image Push to Nexus'){
   sh "docker login -u admin -p admin123 3.111.57.102:8100"
   sh "docker tag priyait1995/myweb:0.0.2 3.111.57.102:8100/priyadevops:1.0.0"
   sh 'docker push 3.111.57.102:8100/priyadevops:1.0.0'
   }

  stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
}

stage('Docker deployment'){
   sh 'docker run -d -p 9898:8080 --name tomcattest priyait1995/myweb:0.0.2' 
   }
}
