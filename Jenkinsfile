pipeline {
    agent any

    // options {
    //     skipDefaultCheckout()
    // }

    tools {
        nodejs '20'
    }

    environment {
        TAG="$env.BRANCH_NAME-v$BUILD_NUMBER"
        REGISTRY="266096842478.dkr.ecr.eu-north-1.amazonaws.com/cloudsec"
        AWS_REGION="eu-north-1"
    }

    stages {
        // stage('Checkout'){
        //     steps{
        //         checkout scmGit(branches: [[name: env.BRANCH_NAME ]], extensions: [], userRemoteConfigs: [[credentialsId: 'github-access', url: 'https://github.com/HL-Abdallah/staff-manager-admin-ui-cloudsec.git']])
        //     }
        // }
        stage('Build React App') {
            steps {
                script {
                    sh "npm install"
                    sh "npm run build"
                }
            }
        }
        stage("Build Docker Image") {
            steps {
                sh "docker build -t staff-manager-admin-ui  ."
            }
        }
        stage('Push to Registry') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'aws-key', variable: 'awsKey'), string(credentialsId: 'aws-secret', variable: 'awsSecret')]) {
                        withEnv(["AWS_ACCESS_KEY_ID=$awsKey", "AWS_SECRET_ACCESS_KEY=$awsSecret", "AWS_DEFAULT_REGION=$AWS_REGION"]) {
                            sh "aws configure list"
                            sh "aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $REGISTRY"
                            sh "docker tag staff-manager-admin-ui $REGISTRY:$TAG"
                            sh "docker push $REGISTRY:$TAG"
                        }
                    }
                }
                
            }
        }
    }
}
