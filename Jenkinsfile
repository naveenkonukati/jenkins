pipeline {
	agent any
		stages {
			stage ('checkout') {
				steps {
				    	git 'https://github.com/naveenkonukati/jenkins.git'
					}
				}
			
				stage('build') {
					steps {
				    	sh 'mvn -f spring-boot-samples/spring-boot-sample-atmosphere/pom.xml clean package'
					}
				}
				stage('archive') {
					steps {
				    	archiveArtifacts artifacts: 'spring-boot-samples/spring-boot-sample-atmosphere/target/*.?ar', followSymlinks: false
					}
				}
				stage('unit tests') {
					steps {
					    junit 'spring-boot-samples/spring-boot-sample-atmosphere/target/surefire-reports/*.xml'
					}
				}
				stage('Nexus Artifact Uploader') {
					steps {
					    nexusArtifactUploader artifacts: [[artifactId: 'spring-boot-sample-atmosphere',
						classifier: '',
						file: 'spring-boot-samples/spring-boot-sample-atmosphere/target/spring-boot-sample-atmosphere-1.4.0.RELEASE.jar',
						type: 'jar']], credentialsId: 'nexusid', 
						groupId: 'org.springframework.boot',
						nexusUrl: '192.168.1.7:8081/nexus',
						nexusVersion: 'nexus2',
						protocol: 'http',
						repository: 'snapshots',
						version: '1.4.0.RELEASE'
					}
					
				}
				
			}
			post {
			always {
				echo 'welcome to devops'
			}
			success {
				echo 'build is success'
			}
			failure {
				echo 'build is failed'
			}
		}
	}

	def notify(status){
		emailext (
			body: "${status}-${env.BUILD_URL}", 
			subject: "JOB:${env.JOB_NAME} with build: ${env.BUILD_ID}${status}", 
			to: 'udu6767@gmail.com'
		)
	}
