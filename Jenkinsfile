pipeline {
    agent any
    environment{
       DOCKER_TAG = getDockerTag()
       KUBECONFIG="/home/darsan/.kube/config"
    }
    stages{
        stage("Checkout to git"){
            steps{
            git branch: 'main', url: 'https://github.com/Darshannraval/MyFastAPI'
            }
        }
        stage("Build Docker Image"){
            steps{
                sh "sudo docker build . -t darshannraval/mytest:${DOCKER_TAG}"
            }
        }
        stage("Docker login and push image to Docker hub"){
            steps{
                withCredentials([string(credentialsId: 'docker_password', variable: 'dockerhub_password')]) {
                  sh "sudo docker login -u darshannraval -p ${dockerhub_password}"
                  sh "sudo docker push darshannraval/mytest:${DOCKER_TAG}"
                  sh "sudo sh /home/darsan/MyFastAPI/changetag.sh ${DOCKER_TAG}"
                }
            }
        }    
        stage("Deploy to Minikube"){
            steps{
                sh "kubectl apply -f /home/darsan/MyFastAPI/mytest.yaml"
                sh "sudo sh /home/darsan/MyFastAPI/redo.sh"
            }    
        }    
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
        