pipeline {
    agent any

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
                    
                }
            }
        }
    }
}