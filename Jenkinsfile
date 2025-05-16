pipeline {
    agent any {


    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/Avenis3010/EUTECH-CICD-PROJECT.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t $DOCKER_HUB_USER/$IMAGE_NAME:$IMAGE_TAG .
                    docker tag $DOCKER_HUB_USER/$IMAGE_NAME:$IMAGE_TAG $DOCKER_HUB_USER/$IMAGE_NAME:latest
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $DOCKER_HUB_USER/$IMAGE_NAME:$IMAGE_TAG
                        docker push $DOCKER_HUB_USER/$IMAGE_NAME:latest
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['EC2_SSH_KEY']) {
                    sh '''
                        echo "Testing SSH connection"
                        ssh -o StrictHostKeyChecking=no @${REMOTE_IP} "echo 'Connected to EC2'"

                        ssh -o StrictHostKeyChecking=no ubuntu@${REMOTE_IP} '
                            docker stop eu-tech || true
                            docker rm eu-tech || true
                            docker pull angel810/eu-tech:latest
                            docker run -d -p 80:80 --name eu-tech angel810/eu-tech:latest
                        '
                    '''
                }
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    sleep 10
                    STATUS=$(curl -s -o /dev/null -w "%{http_code}" http://${REMOTE_IP})
                    if [ "$STATUS" -ne 200 ]; then
                        echo "❌ App is not healthy. Status code: $STATUS"
                        exit 1
                    fi
                    echo "✅ App is healthy!"
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment succeeded!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
}

