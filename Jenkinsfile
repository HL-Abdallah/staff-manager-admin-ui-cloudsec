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
        stage {
            steps {
                echo "workspace is : $WORKSPACE"
            }
        }
    }
}
