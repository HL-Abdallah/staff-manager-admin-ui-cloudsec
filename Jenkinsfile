pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                echo 'Checkout'
            }
        }
        stage('Build') {
            steps {
                echo 'Build'
            }
        }
        stage(name: 'Push to Docker-Registry') {
            steps {
                echo "workspace is : $WORKSPACE"
            }
        }
    }
}
