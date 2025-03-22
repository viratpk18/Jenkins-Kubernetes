pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "viratpk18/docker-app:latest"  // Change to your Docker Hub username
        CONTAINER_NAME = "docker-running-app"
        REGISTRY_CREDENTIALS = "docker_praveen"  // Ensure this exists in Jenkins Credentials
    }

    stages {
        stage('Checkout Code') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-pk', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) { // change the credential id 
                    git url: "https://$GIT_USER:$GIT_TOKEN@github.com/viratpk18/Jenkins-Kubernetes.git", branch: 'main' //change the repo
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_praveen', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Stop & Remove Existing Container') {
            steps {
                script {
                    sh '''
                    if [ "$(docker ps -aq -f name=$CONTAINER_NAME)" ]; then
                        docker stop $CONTAINER_NAME || true
                        docker rm $CONTAINER_NAME || true
                    fi
                    '''
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d -p 5001:5000 --name $CONTAINER_NAME $DOCKER_IMAGE'
            }
        }
    }

    post {
        success {
            echo "✅ Build, push, and deployment successful!"
        }
        failure {
            echo "❌ Build or deployment failed."
        }
    }
}
