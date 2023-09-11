pipeline {

    agent { label 'gp-agent' }
    
    environment {
        TAG="$env.BRANCH_NAME-v$BUILD_NUMBER"
        REGISTRY="266096842478.dkr.ecr.eu-north-1.amazonaws.com/cloudsec"
        AWS_REGION="eu-north-1"
        TAGGED="$REGISTRY/staff-manager-admin-ui:$TAG"
    }

    stages {
        stage('Install dependencies') {
            steps {
                script {
                    sh "npm install"
                }
            }
        }
        stage('Build React App') {
            steps {
                script {
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
                    withEnv(["AWS_DEFAULT_REGION=$AWS_REGION"]) {
                        sh "aws configure list"
                        sh "aws ecr get-login-password | docker login --username AWS --password-stdin $REGISTRY"
                        sh "docker tag staff-manager-admin-ui $TAGGED"
                        if (env.BRANCH_NAME == 'production') {
                            def deployInput = input(
                                id: 'deployToProd',
                                message: 'Do you want to push a new production image ?',
                                parameters: [choice(choices: ['Yes', 'No'], description: 'Choose whether to push or not?', name: 'Push image?')],
                                submitter: 'admin'
                            )
                            if (deployInput == 'Yes') {
                                sh "docker push $TAGGED"
                            } else {
                                echo "Image push skipped by user."
                            }
                        } else {
                            sh "docker push $TAGGED"
                        }
                        // sh "docker push $TAGGED"
                    }
                }
            }
        }
    }
}
