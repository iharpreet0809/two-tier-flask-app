@Library("Shared") _
pipeline {
    agent { label "dev"};

    stages {
        stage('Code Clone') {
            steps {
                // git url: 'https://github.com/iharpreet0809/two-tier-flask-app.git', branch: 'master'
                clone('https://github.com/iharpreet0809/two-tier-flask-app.git', 'master')
            }
        }
        stage('Trivy file system Check'){
            steps{
                // sh "trivy fs . -o results.json"
                trivy()
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
                script{
                    docker_push("dockerHubCreds","two-tier-flask-app")
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

post{
    success{
        emailext(
        subject: "Build Successfully Done for DemoCICD App",
        body: "good news your build was successfull!",
        to:"harpreetarora066@gmail.com, talkwithharpreet@gmail.com"
        )
    }
    failure{
        emailext(
        subject: "Build Failure for DemoCICD App",
        body: "Bad news your build was NOt successfull!",
        to:"harpreetarora066@gmail.com, talkwithharpreet@gmail.com"
        )
    }
}    
}
