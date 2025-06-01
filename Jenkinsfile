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
                sh 'docker compose pull flask-app'
                sh 'docker compose up -d flask-app'
                // sh 'docker compose up -d --build flask-app' //This tries to build locally on EC2, which causes new images to be created and piles up space.
                //Better approach: Pull the latest image from Docker Hub instead of building on EC2. 
                //--build forces building the image locally, which you want to avoid on EC2 (you build on Jenkins agent and push). So just remove it.
                sh 'docker image prune -f' //Add this as a post-deploy step to free up space by removing dangling images:
                //EC2 only pulls the updated image, no new local builds => less disk space usage
                //You always run the exact tested image from your Docker Hub
                //You clear old images and keep EC2 clean
                
            }
        }
    }
}
