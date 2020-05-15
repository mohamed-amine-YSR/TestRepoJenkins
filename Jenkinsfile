// pipline declarative syntaxes
pipeline{
	slackSend botUser: true, channel: 'project1', color: '#0000ff', message: '__________ New COMMIT => Build Start __________', tokenCredentialId: 'slack-token'
	agent any
	//agent{docker {image 'maven:3.6.3'} }

	environment{
		dockerHome = tool 'MyDOCKER'
		mavenHome = tool 'MyMAVEN'
		PATH = "$dockerHome/bin:$PATH"
	}
	stages{
		//CheckOut stage
		stage('CheckOut'){
			steps{
				try {
					withMaven(maven : 'MyMAVEN') {
						bat 'mvn --version'
					}
					//bat 'mvn --version'
					bat 'docker version'
					echo 'Build'
					echo  "Path - $PATH"
					echo  "Build number - $env.BUILD_NUMBER"
					echo  "Build id - $env.BUILD_ID"
					echo  "JOB name - $env.JOB_NAME"
					echo  "Build TAGE - $env.BUILD_TAG"
					echo  "Build url - $env.BUILD_URL"
					slackSend botUser: true, channel: 'project1', color: '#00ff00', message: 'Checkout Step: Success', tokenCredentialId: 'slack-token'
				    }
				    catch(all) {
					slackSend botUser: true, channel: 'project1', color: '#ff0000', message: 'Checkout Step: Failure', tokenCredentialId: 'slack-token'
				    }
				
				
			}
		}

		//Compile Stage
		stage('Compile'){
			steps{
				try {
					withMaven(maven: 'MyMAVEN'){
						bat 'mvn compile'
					}
					slackSend botUser: true, channel: 'project1', color: '#00ff00', message: 'Compile Step: Success', tokenCredentialId: 'slack-token'
				}
				    catch(all) {
					slackSend botUser: true, channel: 'project1', color: '#ff0000', message: 'Compile Step: Failure', tokenCredentialId: 'slack-token'
				    }
			}
		}
		//Integration test stage
		stage('Integration Test'){
			steps{
				try {
					withMaven(maven: 'MyMAVEN'){
						bat 'mvn failsafe:integration-test failsafe:verify'
					}
					slackSend botUser: true, channel: 'project1', color: '#00ff00', message: 'Integration Test Step: Success', tokenCredentialId: 'slack-token'
				}
				    catch(all) {
					slackSend botUser: true, channel: 'project1', color: '#ff0000', message: 'Integration Test Step: Failure', tokenCredentialId: 'slack-token'
				    }
			}
		}
		
		//Packaging stage
		stage('Package'){
			steps{
				try {
					withMaven(maven:'MyMAVEN'){
						bat 'mvn package -DskipTests'
					}
					slackSend botUser: true, channel: 'project1', color: '#00ff00', message: 'Packaging Step: Success', tokenCredentialId: 'slack-token'
				}
				    catch(all) {
					slackSend botUser: true, channel: 'project1', color: '#ff0000', message: 'Packaging Step: Failure', tokenCredentialId: 'slack-token'
				    }
			}
		}

		//Build docker image
		stage('Build docker Image'){
			steps{
				try {
					script{
						dockerImage = docker.build("medamineysr/currency-exchange-devops:${env.BUILD_TAG}")
					}
					slackSend botUser: true, channel: 'project1', color: '#00ff00', message: 'Build Docker image Step: Success', tokenCredentialId: 'slack-token'
				}
				    catch(all) {
					slackSend botUser: true, channel: 'project1', color: '#ff0000', message: 'Build Docker image Step: Failure', tokenCredentialId: 'slack-token'
				    }
			}
		}

		//Push Docker Image
		stage('Push Docker Image'){
			steps{
				try {
					script{
						docker.withRegistry('', 'ID_DOCKER'){
							dockerImage.push();
							dockerImage.push('latest');
						}
					}
					slackSend botUser: true, channel: 'project1', color: '#00ff00', message: 'Push Docker image Step: Success', tokenCredentialId: 'slack-token'
				}
				    catch(all) {
					slackSend botUser: true, channel: 'project1', color: '#ff0000', message: 'Push Docker image Step: Failure', tokenCredentialId: 'slack-token'
				    }
			}
		}
	} 
	post {
		always {
			echo 'The service is running'
		}
		success {
			echo 'The service is running: Success'
			slackSend botUser: true, channel: 'project1', color: '#00ff00', message: 'The service is running: Success', tokenCredentialId: 'slack-token'
		}
		failure {
			echo 'The service is running: Failure'
			slackSend botUser: true, channel: 'project1', color: '#ff0000', message: 'The service is running: Failure', tokenCredentialId: 'slack-token'
		}
	}
	slackSend botUser: true, channel: 'project1', color: '#0000ff', message: '__________ Build Finish __________', tokenCredentialId: 'slack-token'
}
