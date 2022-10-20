pipeline {
    agent any

    stages {
        stage ('Build Docker Image'){
            steps {
                script{
                    dockerapp = docker.build("edsontecno/kube-news:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        stage ('Push Docker Image'){
            steps {
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
                        dockerapp.push('latest')
                    }
                }
            }
        }

        stage ('Deploy kubernets'){
            environment {
                tag_version = "latest"
            }
            steps {
               withKubeConfig([credentialsId: 'kubeconfig']){
                sh 'sed -i "s/{{TAG}}/$tag_version/g" ./k8s/deployment.yaml'
                sh 'kubectl apply -f ./k8s/deployment.yaml'
               }
            }
        }
    }
}