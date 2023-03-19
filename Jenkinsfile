pipeline{
    agent any
    stages{
        stages{
            stage('SONAR: Quality Check'){
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
}