pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = '378339051275'
        app = 'tgp-svc-dashboard'
        IMAGE_TAG = "tgp-svc-dashboard-${BUILD_NUMBER}"
        AWS_ACCESS_KEY_ID = credentials('AWS-Creds')
        AWS_SECRET_ACCESS_KEY = credentials('AWS-Creds')
        AWS_DEFAULT_REGION = 'ap-south-1'
        SONAR_LOGIN = credentials('tokensonar')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: "origin/${env.BRANCH_NAME}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'GITHUB-CONNECTION', url: 'https://git.goodplatform.in/bpsl-nagarro/tgp-svc-dashboard.git']]])
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
            -Dsonar.host.url=http://sonar.bpcl.goodplatform.co:9000 \
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
