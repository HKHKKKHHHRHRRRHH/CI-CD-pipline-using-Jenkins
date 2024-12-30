pipeline{
    agent any 
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '30', numToKeepStr: '2')
    }
    tools{
        maven 'Maven'
    }
    environment{
        cred = credentials('aws-key')
        dockerhub_cred = credentials('docker-cred')
        DOCKER_IMAGE = "yash452009/project-k8s"
        DOCKER_TAG = "$BUILD_NUMBER"
    }
    stages{
        stage('Checkout stage'){
            steps{
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/DevopsWorking/addressbook']])
            }
        }
        stage('Maven Build'){
            steps{
                sh 'mvn package'
            }
        }
        stage('docker build'){
            steps{
                sh 'docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
            }
        }
        stage('docker push'){
            steps{
                sh "echo $dockerhub_cred_PSW | docker login -u  $dockerhub_cred_USR --password-stdin"
                sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
                sh "docker push ${DOCKER_IMAGE}:latest"
            }
        }
        stage('k8s deploy'){
            steps{
                sh 'aws eks update-kubeconfig --region us-east-1 --name devops-working'
                sh 'kubectl apply -f Application.yaml'
            }
        }
        
    }
    post {
            always {
                echo "Job is completed"
            }
            success {
                echo "Job is completed successfully"
            }
            failure {
                echo "Job Failed"
            }
        }
    
}
