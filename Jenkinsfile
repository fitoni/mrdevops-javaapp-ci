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
                            docker build -t 108.137.90.176:8083/springapp:${VERSION} . 
                            docker login -u admin -p $nexus_creds 108.137.90.176:8083
                            docker push 108.137.90.176:8083/springapp:${VERSION}
                            docker rmi 108.137.90.176:8083/springapp:${VERSION}
                        '''
                    }
                }
            }
        }      

        stage('Identifying misconfigurations using Datree in Helm Charts'){
            steps{
                script{
                    dir('kubernetes/myapp/') {
                        withEnv(['DATREE_TOKEN=902b9cb1-1099-4c38-996d-89031576735c']) {
                            sh 'helm datree test .'    
                        }                        
                    }
                }
            }
        }    

        // stage('Pushing the helm chart onto My Own Nexus Repo'){
        //     steps{
        //         script{
        //             withCredentials([string(credentialsId: 'nexus_password', variable: 'nexus_creds')]){
        //                 dir('kubernetes/'){
        //                     sh '''
        //                     helmversion=$(helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ')
        //                     tar -czvf myapp-${helmversion}.tgz myapp/
        //                     curl -u admin:$nexus_creds http://108.136.59.57:8081/repository/devops-helm-hosted/ --upload-file myapp-${helmversion}.tgz -v 
        //                     '''
        //                 }
        //             }
        //         }
        //     }
        // }         
    }
    post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL of the build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "fitoni77@gmail.com";  
		    }
	    }    
}