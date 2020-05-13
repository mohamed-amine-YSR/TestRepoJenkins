// pipline declarative syntaxes
pipeline{
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
			}
		}

		//Compile Stage
		stage('Compile'){
			steps{
				withMaven(maven: 'MyMAVEN'){
					bat 'mvn compile'
				}
			}
		}
		//Integration test stage
		stage('Integration Test'){
			steps{
				withMaven(maven: 'MyMAVEN'){
					bat 'mvn failsafe:integration-test failsafe:verify'
				}
			}
		}
		
		//Packaging stage
		stage('Package'){
			steps{
				withMaven(maven:'MyMAVEN'){
					bat 'mvn package -DskipTests'
				}
			}
		}

		//Build docker image
		stage('Build docker Image'){
			steps{
				script{
					dockerImage = docker.build("medamineysr/currency-exchange-devops:${env.BUILD_TAG}")
				}
			}
		}

		//Push Docker Image
		stage('Push Docker Image'){
			steps{
				script{
					docker.withRegistry('', 'ID_DOCKER'){
						dockerImage.push();
						dockerImage.push('latest');
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
			slackSend channel: 'utilisation-de-lapproche-devops-dans-les-projets-web', color: '#00ff00', message: 'The service is running: Success', tokenCredentialId: 'slack-token'
		}
		failure {
			echo 'The service is running: Failure'
		}
	}
}
