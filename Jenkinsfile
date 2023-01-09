pipeline {
    agent any
    environment{
        VERSION="${env.BUILD_ID}"
    }

    stages{
        stage('Sonar Quality Check'){
            agent{
                docker{
                    image 'maven'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                       // some block
                       sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }

        stage('Quality Gate Status'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }

        stage('Docker build & docker push to My Own Nexus Repo'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus_password', variable: 'nexus_creds')]) {
                        sh ''' 
                            docker build -t 108.136.233.118:8083/springapp:${VERSION} . 
                            docker login -u admin -p $nexus_creds 108.136.233.118:8083
                            docker push 108.136.233.118:8083/springapp:${VERSION}
                            docker rmi 108.136.233.118:8083/springapp:${VERSION}
                        '''
                    }
                }
            }
        }
    }
    post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "fitoni77@gmail.com";  
		}
	}
}