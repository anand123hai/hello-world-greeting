node('master') {
	stage('Poll') {
		checkout scm
}
stage('Build & Unit test'){
	'mvn clean verify -DskipITs=true';
	junit 'C:/anand-master/maven-project/hello-world-greeting/hello-world-greeting/target/surefire-reports/TEST-*.xml'
	archive 'target/*.war'

}
stage('Static Code Analysis'){
	sh 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=$BUILD_NUMBER';
}
stage ('Integration Test'){
	sh 'mvn clean verify -Dsurefire.skip=true';
	junit '**/target/failsafe-reports/TEST-*.xml'
	archive 'target/*.jar'
}
stage ('Publish'){
	def server = Artifactory.server 'Default Artifactory Server'
	def uploadSpec = """{
		"files": [
		{
			"pattern": "target/hello-0.0.1.war",
			"target": "example-project/${BUILD_NUMBER}/",
			"props": "Integration-Tested=Yes;Performance-Tested=No"
		}
	]
	}"""
	server.upload(uploadSpec)
}
}
