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
				stage('nexus artifact') {
					steps {
					    nexusArtifactUploader artifacts: [[artifactId: 'spring-boot-samples',
						classifier: '',
						file: 'spring-boot-samples/spring-boot-sample-atmosphere/target/spring-boot-sample-atmosphere-1.4.0.RELEASE.jar',
						type: 'spring-boot-samples/spring-boot-sample-atmosphere/target/spring-boot-sample-atmosphere-1.4.0.RELEASE.jar']],
						credentialsId: 'nexusidnew',
						groupId: 'org.springframework.boot',
						nexusUrl: '18.191.159.211:8081/nexus',
						nexusVersion: 'nexus2',
						protocol: 'http',
						repository:'releases',
						version: "1.4.${BUILD_NUMBER}"
					}
				}
				stage('Deploy') {
					input {
                				message "Should we continue?"
                				ok "Yes, we should."
            				}
					steps {
						checkout([$class: 'GitSCM', branches: [[name: '*/master']],
						doGenerateSubmoduleConfigurations: false, 
                                		extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'ansible']],
						submoduleCfg: [], 
        					userRemoteConfigs: [[url: 'https://github.com/naveenkonukati/deploy-ansible-tomcat-jenkins.git']]])
				
						withCredentials([string(credentialsId: 'ansi_vault_pass', variable: 'MYPASS')]) {
							sh '''
								echo $MYPASS
								echo $MYPASS > ~/.vault_pass.txt
								export ANSIBLE_VAULT_PASSWORD_FILE=~/.vault_pass.txt
								cd ansible
								sudo ansible-playbook -i production -e "BUILD_NO=${BUILD_NUMBER}" site.yml
							'''
						}
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
