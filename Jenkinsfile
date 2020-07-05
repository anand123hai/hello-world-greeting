node('docker-perf-test-server') {
	stage('Checkout from github') {
		git 'https://github.com/anand123hai/hello-world-greeting.git'
}
stage('Build & Unit test'){
    bat 'mvn clean verify -DskipITs=true';
    junit '**/target/surefire-reports/TEST-*.xml'
    archive 'target/*.war'
}
stage('Static Code Analysis'){
	bat 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=$BUILD_NUMBER';
	//bat 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=0.0.1';
}
stage ('Integration Test'){
	bat 'mvn clean verify -Dsurefire.skip=true';
	junit '**/target/failsafe-reports/TEST-*.xml'
	archive 'target/*.war'
}
stage ('Publish'){
	def server = Artifactory.server 'jfrog-artifactory-server'
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
stash includes:'target/hello-0.0.1.war,src/pt/Hello_World_Test_Plan.jmx',name: 'binary'
}
