pipeline {
    agent any
    environment {
     def mavenHome =tool name:"Maven-3.6.3", type: "maven"
     def mvn ="${mavenHome}/bin/mvn"
	 def ImageName="pat242/spring-boot-mongo-testing"
   }

    stages {
        stage('Git Clone') {
            steps {
                git credentialsId: 'GIT_CREDENTIALS', url: 'https://github.com/pat242/mithun_spring-boot-mongo-docker.git',branch: 'new-feature'
            }
        }
        
        stage("Maven Clean Build"){
            steps{
                sh "${mvn} clean package"
            }
        }
        
        stage("Build Docker Image"){
            steps {
            sh "docker build -t ${ImageName}:${BUILD_NUMBER} ."                      
            }
        }
        stage("Docker Push"){
            steps{
                withCredentials([string(credentialsId: 'DOCKER_HUB_CREDENTIAL', variable: 'DOCKER_HUB_CREDENTIAL')]) {
                    sh "docker login -u pat242 -p ${DOCKER_HUB_CREDENTIAL}"
                }
                sh "docker push ${ImageName}:${BUILD_NUMBER}"
            }
        }
        stage("Deploy Application to K8s Cluster"){
            steps{
                withKubeConfig(credentialsId: 'K8S-KUBECONFIG') {
                sh 'kubectl apply -f springBootMongo.yml'

            }
        }
       
        } 
    
}
}
