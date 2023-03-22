pipeline{
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage('Build Image'){
            agent{
                docker{
                    image 'maven'
                }
            }
            steps{
                script{
                        sh 'mvn clean package sonar:sonar'
                }
            }
        }
//         stage('SONAR: Gate Status'){
//             steps{
//                 script{
//                     waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
//                 }
//             }
//         }
//         stage('Image build & push'){
//             steps{
//                 script{
//                     withCredentials([string(credentialsId: 'nexus_pswd', variable: 'nexus_cred')]) {
//                         sh '''
//                             docker build -t 192.168.56.115:8083/javaapp:${VERSION} .
//                             docker login -u admin -p $nexus_cred 192.168.56.115:8083
//                             docker push 192.168.56.115:8083/javaapp:${VERSION}
//                             docker rmi 192.168.56.115:8083/javaapp:${VERSION}
//                         '''
//                     }
//                 }
//             }
//         }
    }
}
