pipeline {
	environment {
    registry = "amitnike/nginxhello"
    registryCredential = 'dockerhub'
	dockerImage = ''
  	}
	agent any
	stages {

		stage('Lint HTML') {
			steps {
				sh 'tidy -q -e *.html'
			}
		}

		stage('Build Docker Image') {
			steps {
				script{
					dockerImage=docker.build registry + ":$BUILD_NUMBER"
				}
			}
		}

		stage('Push Image to HUB') {
			steps{
				script {
					docker.withRegistry( '', registryCredential ) {
					dockerImage.push()
				}
				}
			}
		}

		stage('Set current kubectl context') {
			steps {
				withAWS(region:'us-west-2', credentials:'ecr_credentials') {
					sh '''
						kubectl config use-context arn:aws:eks:us-west-2:647362336090:cluster/EKS-ZqPJMSLghEmq	
					'''
				}
			}
		}

		stage('Deployment to nodes') {
			steps {
				withAWS(region:'us-west-2', credentials:'ecr_credentials') {
					sh '''
						kubectl apply -f capstone-app-deployment.yml
						kubectl get nodes
						kubectl get pods	
					'''
				}
			}
		}

	}
}