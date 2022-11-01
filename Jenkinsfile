pipeline {
    agent any
    environment{
       DOCKER_TAG = getDockerTag()
       KUBECONFIG="/home/darsan/.kube/config"
    }
    stages{
        stage("checkout to git"){
            steps{
            git branch: 'main', url: 'https://github.com/Darshannraval/MyFastAPI'
            }
        }
        stage("Build Docker Image and Push to Docker Hub"){
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
        stage("deploy to monikube"){
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
        