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
                    - name: maven
                      image: maven:latest
            '''
        }
    }
    stages{
        stage('Maven Build'){
            steps{
                script{
                    sh 'mvn clean package'
                }
            }
        }
    }
}