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
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: "origin/main"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/vikranthshetty2413/springboot-app-deployment-K8S.git']]])
            }
        }
        stage('Code Build') {
            steps {
                sh 'mvn clean install'
            }
        }
       /* stage('Compile and Run Sonar Analysis') {
            steps {
                sh "mvn clean verify admin:admin  \
            -Dsonar.projectKey=frontend \
            -Dsonar.host.url=http:54.179.193.150:9000 \
            -Dsonar.login=sqa_50885d4939be4dfc296b4d5af73a7c307287fec8"
            }
        }
        stage('Push to S3') {
            steps {
                sh 'aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID'
                sh 'aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY'
                sh 'aws configure set default.region $AWS_DEFAULT_REGION'
                sh 'aws s3 ls'
                sh 'aws s3 cp target/*.jar s3://eksfrontendapp/'
                }
            }
               
        stage('Snyk Test') {
            steps {
                echo 'Snyk Testing...'
                snykSecurity (
                    projectName: 'babu517', 
                    snykInstallation: 'snyk@latest', 
                    snykTokenId: 'Snyk',
                    failOnIssues: false
                )
            }
        } */

        stage('Build Image') {
            steps {
                sh 'docker build -t new .'
            }
        }

        stage('Push to S3') {
            steps {
                sh 'aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID'
                sh 'aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY'
                sh 'aws configure set default.region $AWS_DEFAULT_REGION'
                sh 'aws s3 ls'
                sh 'aws s3 cp target/*.jar s3://eksfrontendapp/'
                }
            }
        
        stage('Deploying ECR Image to EKS') {
            steps {
                script {
                    sh '''
                        aws eks update-kubeconfig --name $EKS_CLUSTER_NAME
                        kubectl apply -f eks-deploy-k8s.yaml 
                    '''
                }
            }
        }
        
    }
}














