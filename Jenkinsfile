pipeline {
    agent any

    stages {
        
        stage('Maven Build') {
            steps {
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build') {
            steps {
                sh "docker build . -t nsuryagounder/hiring:${commit_id()}"
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'docker-demo', variable: 'hubPwd')]) {
                    sh "docker login -u nsuryagounder -p ${hubPwd}"
                    sh "docker push nsuryagounder/hiring:${commit_id()}"
                }
            }
        }
        stage('Docker Deploy') {
            steps {
                sshagent(['docker-host']) {
                    sh "ssh -o StrictHostKeyChecking=no  ec2-user@43.207.59.68 docker rm -f hiring"
                    sh "ssh  ec2-user@43.207.59.68 docker run -d -p 8080:8080 --name hiring nsuryagounder/hiring:${commit_id()}"
                }
            }
        }

    }
}

def commit_id(){
    id = sh returnStdout: true, script: 'git rev-parse HEAD'
    return id
}
