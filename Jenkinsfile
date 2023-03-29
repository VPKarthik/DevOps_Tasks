pipeline{
    agent{
        kubernetes{
            yaml '''
                apiVersion: v1
                kind: Pod
                spec:
                  containers:
                    - name: maven
                      image: maven:latest
                      command: ["sleep", "infinity"]
                      tty: true
                      volumeMounts:
                        - name: docker-socket
                          mountPath: /var/run/docker.sock
                    - name: docker
                      image: docker:latest
                      command: ["sleep", "infinity"]
                      tty: true
                      volumeMounts:
                        - name: docker-socket
                          mountPath: /var/run/docker.sock
                  volumes:
                    - name: docker-socket
                      hostPath:
                        path: /var/run/docker.sock
            '''
            /* cleanup: false */
       }
    }
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage('Maven Build'){
            steps{
                script{
                    container('maven'){
                        sh 'mvn clean package'
                    }
                }
            }
        }
        stage('Docker image build'){
            steps{
                script{
                    container('docker'){
                        sh 'docker build -t 192.168.56.115:8083/test:${VERSION} .'
                    }
                }
            }
        }
        stage('Push Docker Image To Nexus Repo'){
            steps{
                script{
                    container('docker'){
                        withCredentials([string(credentialsId: 'nexus_pwd', variable: 'nexus')]) {
                            sh '''
                                docker login -u admin -p $nexus 192.168.56.115:8083
                                docker push 192.168.56.115:8083/test:${VERSION}
                            '''
                        }
                    }
                }
            }
        }
    }
}