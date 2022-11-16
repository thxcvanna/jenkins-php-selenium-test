pipeline {
	agent none
	stages {
		stage('Integration UI Test') {
			// Runs 2 stages in parallel
			parallel {
				// Deploy the PHP server 
				stage('Deploy') {
					agent any
					steps {
						 // Give permissions to run script
                		sh 'chmod +x ./jenkins/scripts/deploy.sh'
						sh './jenkins/scripts/deploy.sh'
						input message: 'Finished using the web site? (Click "Proceed" to continue)'
						sh 'chmod +x ./jenkins/scripts/kill.sh'
						sh './jenkins/scripts/kill.sh'
					}
				}
				// Perform integration and UI test when the server is running
				stage('Headless Browser Test') {
					agent {
						docker {
							image 'maven:3-alpine' 
							args '-v /root/.m2:/root/.m2' 
						}
					}
					steps {
						sh 'mvn -B -DskipTests clean package'
						sh 'mvn test'
					}
					post {
						always {
							junit 'target/surefire-reports/*.xml'
						}
					}
				}
			}
		}
	}
}