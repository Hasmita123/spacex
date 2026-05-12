pipeline{
    agent any

    environment{
        TAG = "${BUILD_ID}"
    }

    stages{

        stage('git clone'){
            steps{
                git 'https://github.com/Hasmita123/spacex.git'
            }
        }

        stage('upload files'){
            steps{
                sh 'aws s3 cp . s3://web-app-project1.aws --recursive'
            }
        }

        stage('docker image'){
            steps{
                sh 'docker build -t web-app:$TAG .'
            }
        }

        stage('push image'){
            steps{
                script{
                    withDockerRegistry([credentialsId: '729a0b31-7b58-496e-8986-da75854a5c45']) {

                        sh 'docker tag web-app:$TAG hasmita123/web-app1:v$TAG'

                        sh 'docker push hasmita123/web-app1:v$TAG'
                    }
                }
            }
        }

        stage('docker container'){
            steps{

                sh 'docker rm -f web-app-container || true'

                sh 'docker run -d --name web-app-container -p 9191:80 web-app:$TAG'
            }
        }
    }

    post{

        success{
            emailext(
                subject: 'Deployment successful',
                body: 'Application deployed successfully. The IP Address with port number is http://13.61.153.32:9090/',
                to: 'siddardha070@gmail.com,hasmita1919@gmail.com'
            )
        }

        failure{
            emailext(
                subject: 'Deployment failed',
                body: 'Application deployment failed.',
                to: 'siddardha070@gmail.com,hasmita1919@gmail.com'
            )
        }
    }
}
