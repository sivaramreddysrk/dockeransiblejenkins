pipeline{
    agent any
    tools {
      maven 'maven'
        }
    environment {
      DOCKER_TAG = getVersion()
    stages{
        stage("scm"){
            steps{
                git credentialsId: 'git', url: 'https://github.com/sivaramreddysrk/dockeransiblejenkins.git'
            }
        }
        stage("build"){
            steps{
                sh "mvn clean package"
            }
        }
        stage("docker build"){
            steps{
                sh "docker build -t sivaramreddysrk/hariapp:${DOCKER_TAG} ."
            }
        }
        stage("docker push"){
            steps{
                withCredentials([string(credentialsId: 'docker-Pwd', variable: 'dockerHubPwd')]) {
                sh "docker login -u sivaramreddysrk -p ${dockerHubPwd}"
                }
                sh "docker push sivaramreddysrk/hariapp:1"
            }
        }
        stage("docker deploy"){
            steps{
                ansiblePlaybook credentialsId: 'ec2-user', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }     
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
