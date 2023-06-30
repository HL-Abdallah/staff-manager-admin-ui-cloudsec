node("ci-node") {
    stage("checkout") {
        checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/mchekini-check-consulting/staff-manager-admin-ui']])
    }

    stage("build") {
        sh "npm install"
        sh "npm run build"
    }

    stage("build-image") {
        sh "sudo docker build -t staff-manager-admin-ui ."
    }

    stage("push-registry") {
        withCredentials([usernamePassword(credentialsId: 'mchekini', usernameVariable: 'username',
                passwordVariable: 'password')]) {
                    sh "sudo docker login -u mchekini -p $password"
                    sh "sudo docker tag staff-manager-admin-ui mchekini/staff-manager-admin-ui:1.0"
                    sh "sudo docker push mchekini/staff-manager-admin-ui:1.0"
                    sh "sudo docker rmi mchekini/staff-manager-admin-ui:1.0"
                    sh "sudo docker rmi staff-manager-admin-ui"
                }
        stash includes: "docker-compose.yml", name: "docker-stash-file"
    }

    node("apps-integration") {
        stage("deploy") {
            unstash "docker-stash-file"
            try {
                sh "sudo docker-compose down"
                sh "sudo docker-compose pull"
                sh "sudo docker-compose up -d"
            } catch(Exception e) {
                print("No container running.")
                sh "sudo docker-compose pull"
                sh "sudo docker-compose up -d"
            }
        }
    }
}