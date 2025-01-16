pipeline {
    agent { label 'ec2-agent' } // Replace with your EC2 agent's label

    environment {
        APP_PORT = "8000" // Replace with your application port
        CONTAINER_NAME = "nodejs_app" // Replace with your container name
        IMAGE_NAME = 'node-app' // Replace with your desired image name
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning repository...'
                git branch: 'main', url: 'https://github.com/techpatel197/kp_shark_project.git'
                // Replace with your GitHub repository URL and branch name
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Remove any running container using the specified port
                    sh """
                    docker ps --filter "name=$CONTAINER_NAME" -q | xargs -r docker stop
                    docker ps --filter "name=$CONTAINER_NAME" -q | xargs -r docker rm
                    """

                    // Remove old images if necessary
                    sh """
                    docker images --filter "reference=$IMAGE_NAME" -q | xargs -r docker rmi -f
                    """

                    // Build a new Docker image
                    sh """
                    cd node_project
                    docker build -t $IMAGE_NAME .
                    """
                }
            }
        }

        stage('Deploy to EC2 Agent') {
            steps {
                echo 'Deploying on EC2 agent...'
                script {
                    // Run the container
                    sh """
                    docker run -d --name $CONTAINER_NAME -p $APP_PORT:$APP_PORT $IMAGE_NAME
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
