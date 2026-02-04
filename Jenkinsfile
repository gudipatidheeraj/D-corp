pipeline {
    agent any
        environment {
            IMAGE_NAME = "d-corp"
            IMAGE_TAG = "${BUILD_NUMBER}"
            CONTAINER_NAME = "dcorp-${BUILD_NUMBER}"
            TEST_PORT = "8003"
            ECR_URL = "818988015178.dkr.ecr.ap-south-1.amazonaws.com/d-corp"
            AWS_REGION = "ap-south-1"
        }
        options {
            timeout(time: 40,unit: 'MINUTES')
            timestamps()
        }
    stages {
        stage("Build") {
            steps {
                echo "This is the Build stage used using dynamic declaring"
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ProjectOne/app"
            }
        }
        stage("Test") {
            steps {
            sh """
            docker run -d --network jenkins-net --name ${CONTAINER_NAME} ${IMAGE_NAME}:${IMAGE_TAG}
            sleep 5
            curl -f http://${CONTAINER_NAME}/index/status
            """
            }
        }

        stage("Push to ECR") {
            steps {
                echo "Pushing the image to ECR repository."
                sh """
                set -e
                echo "Logging into Amazon AWS ECR..."
                aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_URL}
                echo "Tagging the image..."
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${ECR_URL}:${IMAGE_TAG}
                echo "Pushing the image to ECR..."
                docker push ${ECR_URL}:${IMAGE_TAG}
                """
            }
        }
        stage("Deploy") {
            steps {
                echo "This is the Deploy stage. No actual deployment is done here."
            }
        }
    }
    post {
        always {
            sh """
                echo "Post stage always stop and remove the container and image!"
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true
                """
        }
        success {
            echo "Pipeline execution completed"
        }
        failure {
            echo "Pipeline execution failed."
        }
    }
}
