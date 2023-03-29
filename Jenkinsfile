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
                      tty: true
                      command: ["sleep", "infinity"]
                    - name: docker
                      image: docker:latest
                      tty: true
                      command: ["sleep", "infinity"]
            '''
            /* cleanup: never */
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
    }
}