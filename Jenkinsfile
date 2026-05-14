pipeline {
    agent any

    environment {
        IMAGE_NAME = "web-app"
        TAG = "build-${BUILD_NUMBER}"
        PUBLIC_IP = "13.63.20.49"
    }

    stages {

        stage('Git Clone') {
            steps {
                git 'https://github.com/Hasmita123/spacex.git'
            }
        }

        stage('Upload to S3') {
            steps {
                sh 'aws s3 cp . s3://web-app-project1.aws --recursive --exclude ".git/*"'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${TAG} ."
            }
        }

        stage('Docker Push') {
            steps {

                withDockerRegistry(
                    [credentialsId: '729a0b31-7b58-496e-8986-da75854a5c45',
                     url: 'https://index.docker.io/v1/']) {

                    sh "docker tag ${IMAGE_NAME}:${TAG} hasmita123/${IMAGE_NAME}:${TAG}"

                    sh "docker push hasmita123/${IMAGE_NAME}:${TAG}"
                }
            }
        }

        stage('Docker Run') {
            steps {

                sh 'docker rm -f web-app-container >/dev/null 2>&1 || true'

                sh "docker run -d --name web-app-container -p 9292:80 hasmita123/${IMAGE_NAME}:${TAG}"
            }
        }
    }

    post {

        success {

            emailext(
                to: 'siddardha070@gmail.com,hasmita1919@gmail.com',

                subject: "Build Success - ${JOB_NAME} #${BUILD_NUMBER}",

                body: """
Application deployed successfully.

Job Name: ${JOB_NAME}
Build Number: ${BUILD_NUMBER}

Docker Image:
hasmita123/${IMAGE_NAME}:${TAG}

Access Application:
http://${PUBLIC_IP}:9292
""",

                mimeType: 'text/plain',
                attachLog: true
            )
        }

        failure {

            emailext(
                to: 'siddardha070@gmail.com,hasmita1919@gmail.com',

                subject: "Build Failed - ${JOB_NAME} #${BUILD_NUMBER}",

                body: """
Build failed.

Job Name: ${JOB_NAME}
Build Number: ${BUILD_NUMBER}

Server IP:
${PUBLIC_IP}

Check Jenkins Console Output for errors.
""",

                mimeType: 'text/plain',
                attachLog: true
            )
        }
    }
}
