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
                sh 'export AWS_SESSION_TOKEN="IQoJb3JpZ2luX2VjEPb//////////wEaCXVzLWVhc3QtMSJGMEQCIBJb2hGThH35D55x6mMHSEd69/4QX//FPYYSjkFvKASSAiAx1Ew/GOPoyJW
FBrSK19vIuX8unwUAhJKFkPiHAEbQpyqUAwgvEAMaDDQ5MDE2NzY2OTk0MCIMWk+A+EgBogdnrU/FKvECHdRjH3plUzKzi594ti6Lj2z1fIFhR6p5R72qEY7mHVAwbEeuf0K2yyER1swXNm84UdzpdRjPMMIMMrGivcSkot
GZtDzfUSllBkvE05e/ncDTUY+1LHvK20OhhDB97EmN8gUuR8dqBKVlA74naABNgsCc8yjwYCBzW6Xk4EWPkRP9Q5/lqkTSRo56uN9oF74xhtUNlQUVNsJOl8834Qf2S43gN/vKzxcjQsSSQeKbJmMTrqKSoYe4Kno0JgRcE
g+HGMmRrea28kII8eRxz302X+WegrKxgxi6c2CYCuO5RB21tLWi93aXwVkMwYfuXWfBnImoJXwoeXUKIe0ENvUUTWav+hBovh9S9j3kI/SXNoWYpeYdLAtINXpNtHMQK4nI7ehMaJbi/T/tZSbJ+x6zeoEviNiEEQ1M7zUo
px9A6CiI0G/tj1YSjzt/UKCBHes629YQef9avRO6L2hpYPwFIYs5kH06T5IY1DSocp7pBmqpMJzIjqoGOqcBtIhzogh+OlV9xeGlI0TEW1UJt9LiLVl2HUQLuKeLRkNTR+G8IFPMp7QM1v9xdn0ILhAfILulKONROf50SHw
Z/y1BQnErnesOZGvlzXvL8l7hWnWqUT7nZOd2L3kMKenBneoLctXZ85I2tYJQsbFmIB9bd+QQwQKbfeuBzmvzO4PvA5xvwJUSnYFYmogASSC4QY6qEw87xjbBmusFGQNawy9sStJv+SI="'
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
                sh 'docker build -t frontendapp-${app} .'
            }
        }
        stage('Push to ECR') {
            steps {
                sh 'aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 490167669940.dkr.ecr.ap-southeast-1.amazonaws.com'
                sh "docker tag frontendapp-${app}:latest  490167669940.dkr.ecr.ap-southeast-1.amazonaws.com/eks-frontend-app-deployment:${app}-${BUILD_NUMBER}"
                sh "docker push 490167669940.dkr.ecr.ap-southeast-1.amazonaws.com/eks-frontend-app-deployment:${app}-${BUILD_NUMBER}"
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





































