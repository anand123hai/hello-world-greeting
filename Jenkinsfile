node('maven-docker-build-slave') {
	stage('Checkout from github') {
		git credentialsId: 'github-credentials-anand123hai', url: 'https://github.com/anand123hai/hello-world-greeting.git'
}
stage('Build & Unit test'){
    sh 'mvn clean verify -DskipITs=true';
    junit '**/target/surefire-reports/TEST-*.xml'
    archiveArtifacts 'target/*.war'
}
stage('Static Code Analysis'){
	sh 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=$BUILD_NUMBER';
	//bat 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=0.0.1';
}
stage ('Integration Test'){
	sh 'mvn clean verify -Dsurefire.skip=true';
	junit '**/target/failsafe-reports/TEST-*.xml'
	archiveArtifacts 'target/*.war'
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
