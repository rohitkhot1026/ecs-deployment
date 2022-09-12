pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="274213768634"
        AWS_DEFAULT_REGION="ap-south-1" 
        CLUSTER_NAME="demo"
        SERVICE_NAME="demo-service"
        TASK_DEFINITION_NAME="demo-task"
        DESIRED_COUNT="1"
        IMAGE_REPO_NAME="myrepo"
        IMAGE_TAG="${env.BUILD_ID}"
        REPOSITORY_URI = "274213768634.dkr.ecr.ap-south-1.amazonaws.com/myrepo:latest"
        ECR_REGISTRY = '274213768634.dkr.ecr.ap-south-1.amazonaws.com/myrepo'
        PATH = "/usr/local/bin/:${env.PATH}"
    }
    
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/Aashumesh/ecr-ecs-cluster.git']]])     
            }
        }
    

        stage('Build Docker Image') {
            steps {
                sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 274213768634.dkr.ecr.ap-south-1.amazonaws.com
                sh 'docker build -t myrepo .'
                sh 'docker image ls'
            }
        }
        stage('Push Image to ECR Repo') {
            steps {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin --password-stdin 220080856178.dkr.ecr.us-east-1.amazonaws.com'
                sh 'docker tag 274213768634.dkr.ecr.ap-south-1.amazonaws.com/myrepo:latest myrepo:latest'
                sh 'docker push 274213768634.dkr.ecr.ap-south-1.amazonaws.com/myrepo:latest'
                
            }
        }
        
        stage('Deploy on Docker Machine') {
            steps {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 274213768634.dkr.ecr.ap-south-1.amazonaws.com'
                sh 'docker pull 274213768634.dkr.ecr.ap-south-1.amazonaws.com/myrepo:latest'
                sh 'docker rm -f mypythonContainer | echo "there is no docker container named todo"'
                sh 'docker run --name mypythonContainer -dp 8096:5000 274213768634.dkr.ecr.ap-south-1.amazonaws.com/myrepo:latest'
            }
        }
        
    }
}
