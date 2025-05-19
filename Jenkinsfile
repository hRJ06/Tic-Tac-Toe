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
                    passwordVariable: 'DOCKERHUB_PASSWORD')]) {

                    sh "docker login -u ${DOCKERHUB_USER} -p ${DOCKERHUB_PASSWORD}"
                    sh "docker tag tic-tac-toe:latest ${DOCKERHUB_USER}/tic-tac-toe:latest"
                    sh "docker push ${DOCKERHUB_USER}/tic-tac-toe:latest"
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
                    passwordVariable: 'DOCKERHUB_PASSWORD')]) {

                    sh "docker run -d --name tic-tac-toe -p 80:80 ${DOCKERHUB_USER}/tic-tac-toe:latest"
                }
            }
        }
    }

    post {
        success {
            emailext(
                from: "${env.MAIL_FROM}",
                to:   "${env.MAIL_TO}",
                subject: "Build #${env.BUILD_NUMBER} SUCCESS – ${env.JOB_NAME}",
                body: """Build succeeded.
                        Job: ${env.JOB_NAME}
                        Build: #${env.BUILD_NUMBER}
                        Link: ${env.BUILD_URL}
                    """
            )
        }

        failure {
            emailext(
                from: "${env.MAIL_FROM}",
                to:   "${env.MAIL_TO}",
                subject: "Build #${env.BUILD_NUMBER} FAILED – ${env.JOB_NAME}",
                body: """Build failed.
                        Job: ${env.JOB_NAME}
                        Build: #${env.BUILD_NUMBER}
                        Check console: ${env.BUILD_URL}console
                    """
            )
        }
    }
}
