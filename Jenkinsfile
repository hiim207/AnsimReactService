pipeline {
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-token')
    }
    agent any
    triggers {
        pollSCM('* * * * *')
    }
    stages {
        stage("Build") {
           steps {
               sh "npm install"
               sh "npm run build"
               sh "cp -r ./build ./docker/apache2/"
           } 
        }
        stage("Docker Login") {
           steps {
               sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin" 
           }
        }
        stage("Docker Image Build") {
           steps {
               sh "docker build -t lee20jin/apache2_ansimreact:${BUILD_NUMBER} ./docker/apache2/"
           }
        }
        stage("Docker Image Push") {
           steps {
               sh "docker push lee20jin/apache2_ansimreact:${BUILD_NUMBER}"
           } 
        }
        stage("Docker Image Clean up") {
           steps {
               sh "docker image rm lee20jin/apache2_ansimreact:${BUILD_NUMBER}" 
           }
        }
        stage("Minikube start") {
           steps {
               sh "minikube start --driver=docker --cni=calico"
           }
        }
        stage("Deploy") {
           steps {
               sh "sed -i 's/{{VERSION}}/${BUILD_NUMBER}/g' ./kubernetes/apache2.yml"
               sh "kubectl delete -A ValidatingWebhookConfiguration ingress-nginx-admission"
               sh "kubectl apply -f ./kubernetes/apache2.yml"
               sh "kubectl apply -f ./kubernetes/ingress.yml"
           } 
           post {
                success {
                    slackSend (
                        channel: "#jenkins",
                        color: "#2C953C",
                        message: "ansimreact 배포가 성공하였습니다.????"
                    )
                    echo "Completed Server Deploy"
                }
                failure {
                    slackSend (
                        channel: "#jenkins",
                        color: "#FF3232",
                        message: "ansimreact 배포가 실패하였습니다."
                    )
                    echo "Fail Server Deploy"
                }
          }
       }  
    }
}
