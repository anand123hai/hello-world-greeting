node('master') {
	stage('Checkout from github') {
		git credentialsId: 'github-credentials-anand123hai', url: 'https://github.com/anand123hai/hello-world-greeting.git'
}
stage('Build & Unit test'){
    bat 'mvn clean verify -DskipITs=true';
    junit '**/target/surefire-reports/TEST-*.xml'
    archiveArtifacts 'target/*.war'
}
stage('Static Code Analysis'){
	bat 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=$BUILD_NUMBER';
	//bat 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=0.0.1';
}
stage ('Integration Test'){
	bat 'mvn clean verify -Dsurefire.skip=true';
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

node('docker-perf-test-server') {
	stage ('Start Tomcat'){
	sh '''cd /home/jenkins/tomcat/bin
	./startup.sh''';
}
stage ('Deploy to Tomcat'){
	unstash 'binary'
	sh 'cp target/hello-0.0.1.war /home/jenkins/tomcat/webapps/';
    }

stage ('Performance Testing-Jmeter'){
	sh '''cd /opt/jmeter/bin
	./jmeter.sh -n -t $WORKSPACE/src/pt/Hello_World_Test_Plan.jmx -l $WORKSPACE/test_report.jtl''';
	step([$class: 'ArtifactArchiver', artifacts: '**/*.jtl'])
}
}
