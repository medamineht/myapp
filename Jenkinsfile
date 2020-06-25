
node {
   // This is to demo github action	
   def sonarUrl = 'sonar.host.url=http://192.168.224.154:9000'
   def mvn = tool (name: 'maven', type: 'maven') + '/bin/mvn'
   stage('SCM Checkout'){
    // Clone repo
	git branch: 'master', 
	credentialsId: 'github', 
	url: 'https://github.com/medamineht/myapp'
   
   }
   
   stage('Sonar Publish'){
	   withCredentials([string(credentialsId: 'sonarqube', variable: 'sonarToken')]) {
        def sonarToken = "sonar.login=${sonarToken}"
        sh "${mvn} sonar:sonar -D${sonarUrl}  -D${sonarToken}"
	 }
      
   }
   
	
   stage('Mvn Package'){
	   // Build using maven
	   
	   sh "${mvn} clean compile package deploy"
   }
   
   stage('deploy-dev'){
       def tomcatDevIp = '192.168.224.153'
	   def tomcatHome = '/opt/apache-tomcat-8.5.50/'
	   def webApps = tomcatHome+'webapps/'
	   def tomcatStart = "${tomcatHome}bin/startup.sh"
	   def tomcatStop = "${tomcatHome}bin/shutdown.sh"
	   sh "scp -o StrictHostKeyChecking=no target/myweb*.war devops@${tomcatDevIp}:${webApps}myweb.war"
      sh "ssh devops@${tomcatDevIp} ${tomcatStop}"
		sh "ssh devops@${tomcatDevIp} ${tomcatStart}"

   }
   stage('Email Notification'){
		mail bcc: '', body: """Hi Team, You build successfully deployed
		                       Job URL : ${env.JOB_URL}
							   Job Name: ${env.JOB_NAME}

Thanks,
DevOps Team""", cc: '', from: '', replyTo: '', subject: "${env.JOB_NAME} Success", to: 'amincssmilan@gmail.com'
   
   }
}

