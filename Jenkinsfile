node{
   stage('SCM Checkout'){
     git 'https://github.com/vanithasanju/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newappgreens.war'
   }
   stage('SonarQube analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
    stage('Build Docker Imager'){
   sh 'docker build -t vandock21/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u vandock21 -p ${dockerPassword}"
    }
   sh 'docker push vandock21/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
       withCredentials([string(credentialsId: 'admin', variable: 'nexusPassword')]) {
   sh "docker login -u admin -p ${nexusPassword} 3.7.69.125:8083"
   sh "docker tag vandock21/myweb:0.0.2 3.7.69.125:8083/nexusvan:1.0.0"
   sh 'docker push 3.7.69.125:8083/nexusvan:1.0.0'
   }
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest saidamo/myweb:0.0.2' 
   }
}
   
}
