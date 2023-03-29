pipeline{
    agent{
        kubernetes{
            yaml '''
                apiVersion: v1
                kind: Pod
                spec:
                  containers:
                    - name: docker
                      image: docker:latest
                      tty: true
                    - name: maven
                      image: maven:latest
                      tty: true
            '''
        }
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
    }
}