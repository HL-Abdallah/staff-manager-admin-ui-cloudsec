node {
    checkout scm

    def TAG = env.BRANCH_NAME + "-v" + env.BUILD_NUMBER
    def REGISTRY = "266096842478.dkr.ecr.eu-north-1.amazonaws.com/cloudsec"
    def AWS_REGION = "eu-north-1"

    stage('Build React App') {
        sh "npm ci"
        sh "npm run build"
    }

    stage("Build Docker Image") {
        sh "docker build -t staff-manager-admin-ui  ."
    }

    stage('Push to Registry') {
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
