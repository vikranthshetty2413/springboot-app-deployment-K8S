pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = '490167669940'
        app = 'tgp-svc-dashboard'
        IMAGE_TAG = "tgp-svc-dashboard-${BUILD_NUMBER}"
        AWS_ACCESS_KEY_ID = credentials('harish credentials')
        AWS_SECRET_ACCESS_KEY = credentials('harish credentials')
        AWS_DEFAULT_REGION = 'ap-southeast-1'
        SONAR_LOGIN = credentials('Sonar-Creds')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: "origin/${env.BRANCH_NAME}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/babu517/springboot-app-deployment-K8S/tree/main']]])
            }
        }
        stage('Code Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Compile and Run Sonar Analysis') {
            steps {
                sh "mvn test jacoco:report sonar:sonar \
            -Dsonar.projectKey=tgp-svc-sonar \
            -Dsonar.host.url=http://18.136.72.199:9000/maintenance?return_to=%2F \
            -Dsonar.login=${SONAR_LOGIN}"
            }
        }
        stage('Push to S3') {
            steps {
                sh 'aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID'
                sh 'aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY'
                sh 'aws configure set default.region $AWS_DEFAULT_REGION'
                sh 'aws s3 ls'
                sh 'aws s3 cp target/*.jar s3://backend-main-bucket/tgp-svc-dashboard/'
            }
        }
        stage('Build Image') {
            steps {
                sh 'docker build -t tgp-svc-dashboard-${app} .'
            }
        }
        stage('Push to ECR') {
            steps {
                sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 378339051275.dkr.ecr.ap-south-1.amazonaws.com'
                sh "docker tag tgp-svc-dashboard-${app}:latest 378339051275.dkr.ecr.ap-south-1.amazonaws.com/tgp-svc-dashboard:${app}-${BUILD_NUMBER}"
                sh "docker push 378339051275.dkr.ecr.ap-south-1.amazonaws.com/tgp-svc-dashboard:${app}-${BUILD_NUMBER}"
            }
        }
    }
}
