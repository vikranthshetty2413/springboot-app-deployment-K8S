pipeline {
    agent any
    environment {
        app = 'frontend'
        IMAGE_TAG = "frontend-${BUILD_NUMBER}"
        AWS_ACCESS_KEY_ID = credentials('AWS-CRED')
        AWS_SECRET_ACCESS_KEY = credentials('AWS-CRED')
        AWS_DEFAULT_REGION = 'ap-southeast-1'
        EKS_CLUSTER_NAME = 'sandboxeks1'
        SONAR_LOGIN = credentials('Sonar-Creds')
    }

    
    stages {
        stage('Checkout in dev ') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: "origin/dev"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/vikranthshetty2413/springboot-app-deployment-K8S.git']]])
            }
        }
        stage('Code Build in dev') {
            when {
                branch 'dev'
            }
            steps {
                sh 'mvn clean install'
            }
        }
    

        stage('Build Image') {
            when {
                branch 'dev'
            }    
            steps {
                sh 'docker build -t new .'
            }
        }
        stage('Push to ECR') {
        when {
                branch 'dev'
            } 
    
            steps {
                sh 'aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 490167669940.dkr.ecr.ap-southeast-1.amazonaws.com'
                sh "docker tag frontendapp-${app}:latest  490167669940.dkr.ecr.ap-southeast-1.amazonaws.com/eks-frontend-app-deployment:${app}-${BUILD_NUMBER}"
                sh "docker push 490167669940.dkr.ecr.ap-southeast-1.amazonaws.com/eks-frontend-app-deployment:${app}-${BUILD_NUMBER}"
            }
        }
        
        
    }
}
