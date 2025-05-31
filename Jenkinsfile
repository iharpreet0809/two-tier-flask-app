pipeline {
    agent any

    stages {
        stage('Code Clone') {
            steps {
                git url: 'https://github.com/iharpreet0809/two-tier-flask-app.git', branch: 'master'
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t two-tier-flask-app .'
            }
        }

        stage('Test') {
            steps {
                echo 'Developer/Tester likh ke dega'
            }
        }

        stage('Push to Docker hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerHubCreds',
                    passwordVariable: 'dockerHubPass',
                    usernameVariable: 'dockerHubUser'
                )]) {
                    sh 'docker login -u $dockerHubUser -p $dockerHubPass'
                    sh 'docker image tag two-tier-flask-app $dockerHubUser/two-tier-flask-app'
                    sh 'docker push $dockerHubUser/two-tier-flask-app:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose up -d --build flask-app'
            }
        }
    }
}
