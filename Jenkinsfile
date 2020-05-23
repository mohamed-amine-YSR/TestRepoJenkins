// pipline declarative syntaxes
pipeline{
	agent any
	//agent{docker {image 'maven:3.6.3'} }

	environment{
		dockerHome = tool 'MyDOCKER'
		mavenHome = tool 'MyMAVEN'
		PATH = "$dockerHome/bin:$PATH"
		channelID = "G013KUVQ77G"
	}
	stages{
		//CheckOut stage
		stage('CheckOut'){
			steps{
				script {
				try {
					slackSend botUser: true, channel: "${channelID}", color: '#0000ff', message: '__________ New COMMIT => Build Start __________', tokenCredentialId: 'slack-token'
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
					slackSend botUser: true, channel: "${channelID}", color: '#00ff00', message: 'Checkout Step: Success', tokenCredentialId: 'slack-token'
				    }
				    catch(all) {
					slackSend botUser: true, channel: "${channelID}", color: '#ff0000', message: 'Checkout Step: Failure', tokenCredentialId: 'slack-token'
				    	error "This pipeline stops here!"
				    }
				}
				
			}
		}

		//Compile Stage
		stage('Compile'){
			steps{
				script {
				try {
					withMaven(maven: 'MyMAVEN'){
						bat 'mvn compile'
					}
					slackSend botUser: true, channel: "${channelID}", color: '#00ff00', message: 'Compile Step: Success', tokenCredentialId: 'slack-token'
				}
				    catch(all) {
					slackSend botUser: true, channel: "${channelID}", color: '#ff0000', message: 'Compile Step: Failure', tokenCredentialId: 'slack-token'
				    	error "This pipeline stops here!"
				    }
				}
			}
		}
		//Integration test stage
		stage('Integration Test'){
			steps{
				script {
				try {
					withMaven(maven: 'MyMAVEN'){
						bat 'mvn failsafe:integration-test failsafe:verify'
					}
					slackSend botUser: true, channel: "${channelID}", color: '#00ff00', message: 'Integration Test Step: Success', tokenCredentialId: 'slack-token'
				}
				    catch(all) {
					slackSend botUser: true, channel: "${channelID}", color: '#ff0000', message: 'Integration Test Step: Failure', tokenCredentialId: 'slack-token'
				    	error "This pipeline stops here!"
				    }
				}
			}
		}
		
		//Packaging stage
		stage('Package'){
			steps{
				script{
				try {
					withMaven(maven:'MyMAVEN'){
						bat 'mvn package -DskipTests'
					}
					slackSend botUser: true, channel: "${channelID}", color: '#00ff00', message: 'Packaging Step: Success', tokenCredentialId: 'slack-token'
				}
				    catch(all) {
					slackSend botUser: true, channel: "${channelID}", color: '#ff0000', message: 'Packaging Step: Failure', tokenCredentialId: 'slack-token'
				    	error "This pipeline stops here!"
				    }
				}
			}
		}

		//Build docker image
		stage('Build docker Image'){
			steps{
				script{
				try {
					dockerImage = docker.build("medamineysr/currency-exchange-devops:${env.BUILD_TAG}")
					slackSend botUser: true, channel: "${channelID}", color: '#00ff00', message: 'Build Docker image Step: Success', tokenCredentialId: 'slack-token'
				}
				catch(all) {
					slackSend botUser: true, channel: "${channelID}", color: '#ff0000', message: 'Build Docker image Step: Failure', tokenCredentialId: 'slack-token'
					error "This pipeline stops here!"    
				   }
				}
			}
		}

		//Push Docker Image
		stage('Push Docker Image'){
			steps{
				script{
				try {
					docker.withRegistry('', 'ID_DOCKER'){
					dockerImage.push();
					dockerImage.push('latest');
					}
					slackSend botUser: true, channel: "${channelID}", color: '#00ff00', message: 'Push Docker image Step: Success', tokenCredentialId: 'slack-token'
				}
				 catch(all) {
					slackSend botUser: true, channel: "${channelID}", color: '#ff0000', message: 'Push Docker image Step: Failure', tokenCredentialId: 'slack-token'
				  	error "This pipeline stops here!" 
				  }
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
			slackSend botUser: true, channel: "${channelID}", color: '#00ff00', message: 'The service is running: Success', tokenCredentialId: 'slack-token'
			slackSend botUser: true, channel: "${channelID}", color: '#0000ff', message: '__________ Build Finish __________', tokenCredentialId: 'slack-token'
		}
		failure {
			echo 'The service is running: Failure'
			slackSend botUser: true, channel: "${channelID}", color: '#ff0000', message: 'The service is running: Failure', tokenCredentialId: 'slack-token'
			slackSend botUser: true, channel: "${channelID}", color: '#0000ff', message: '__________ Build Finish __________', tokenCredentialId: 'slack-token'
		}
	}
	
}
