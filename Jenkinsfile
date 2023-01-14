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
    }
}