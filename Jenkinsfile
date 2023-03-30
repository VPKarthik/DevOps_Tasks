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
        stage('Push Image to DockerHub'){
            steps{
                script{
                    container('docker'){
                        withCredentials([string(credentialsId: 'docker_auth', variable: 'docker_pwd')]) {
                            sh '''
                                docker login -u vpkarthikhosamane -p ${docker_pwd}
                                docker tag 192.168.56.115:8083/test:${VERSION} vpkarthikhosamane/test:${VERSION}
                                docker push vpkarthikhosamane/test:${VERSION}
                            '''
                        }
                    }
                }
            }
        }
        stage('Remove Local Image'){
            steps{
                script{
                    container('docker'){
                        sh '''
                            docker rmi vpkarthikhosamane/test:${VERSION}
                            docker rmi 192.168.56.115:8083/test:${VERSION}
                        '''
                    }
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                script{
                    container('docker'){
                        kubernetesDeploy (configs: 'https://github.com/VPKarthik/DevOps_Tasks/blob/main/deployment-service.yml', kubeconfigId: 'kube-config-dir')
                    }
                }
            }
        }
    }
}