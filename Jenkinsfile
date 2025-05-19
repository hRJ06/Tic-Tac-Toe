@Library("Shared Library") _
pipeline {
    agent { label "DEV" }

    environment {
        MAIL_FROM = "${MAIL_FROM}"
        MAIL_TO   = "${MAIL_TO}"
    }

    stages {
        stage('Code') {
            steps {
                echo "Stage 1 – Code"
                clone("https://github.com/hRJ06/Tic-Tac-Toe.git", "main")
            }
        }

        stage('Build') {
            steps {
                docker_build("tic-tac-toe")
            }
        }

        stage('Test') {
            steps {
                echo "Stage 3 – Test"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                docker_push("dockerHubCredentials", "tic-tac-toe")
            }
        }

        stage('Deploy') {
            steps {
               deploy('dockerHubCredentials', 'tic-tac-toe', 'tic-tac-toe', '80:80')
            }
        }
    }

    post {
        success {
            send_email("success")
        }

        failure {
            send_email("failure")
        }
    }
}
