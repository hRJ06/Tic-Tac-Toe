pipeline {
    agent { label "DEV" }

    stages {

        stage('Code') {
            steps {
                echo "Stage 1 – Code"
                git url: 'https://github.com/hRJ06/Tic-Tac-Toe.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                echo "Stage 2 – Build"
                sh "docker build -t tic-tac-toe:latest ."
            }
        }

        stage('Test') {
            steps {
                echo "Stage 3 – Test"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerHubCredentials',
                    usernameVariable: 'DOCKERHUB_USER',
                    passwordVariable: 'DOCKERHUB_PASSWORD'
                )]) {
                    sh "docker login -u ${env.DOCKERHUB_USER} -p ${env.DOCKERHUB_PASSWORD}"
                    sh "docker tag tic-tac-toe:latest ${env.DOCKERHUB_USER}/tic-tac-toe:latest"
                    sh "docker push ${env.DOCKERHUB_USER}/tic-tac-toe:latest"
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Stage 4 – Deploy"
                sh "docker container rm -f tic-tac-toe || true"
                withCredentials([usernamePassword(
                    credentialsId: 'dockerHubCredentials',
                    usernameVariable: 'DOCKERHUB_USER',
                    passwordVariable: 'DOCKERHUB_PASSWORD'
                )]) {
                    sh "docker run -d --name tic-tac-toe -p 80:80 ${env.DOCKERHUB_USER}/tic-tac-toe:latest"
                }
            }
        }
    }
}
