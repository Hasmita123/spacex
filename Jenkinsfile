pipeline{
    agent any
    environment{
	TAG="${BUILD_ID}"
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
                    withDockerRegistry(credentialsId: 'd24ba0aa-eb82-42c5-8194-6d8c2e48853d') {
		                sh 'docker tag web-app:$TAG hasmita123/web-app1:v$TAG'

                        sh 'docker push hasmita123/web-app1:v$TAG'
                    }
                }
            }
        }
		stage('docker container'){
            steps{
                sh 'docker run -d -p 9090:80 web-app:$TAG'
            }
        }
    }
    post{
        success{
	        emailext(
	            subject:'Deployment successful',
		    	body:'Application deployed successfully. The IP Address with port number is http://13.61.153.32:80/',
		    	to:'siddardha070@gmail.com,hasmita1919@gmail.com'
	    	)
		}
		failure{
	    	emailext(
	        	subject:'Deployment failed',
            	body:'Application deployed failed.',
            	to:'siddardha070@gmail.com,hasmita1919@gmail.com'
	    	)
		}
	}
}
