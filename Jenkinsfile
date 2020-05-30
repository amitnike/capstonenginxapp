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
					dockerImage=docker.build registry + ":latest"
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
						aws eks update-kubeconfig --name EKS-CXFL470QkkLM
						/home/ubuntu/bin/kubectl apply -f aws-auth-cm.yaml
						/home/ubuntu/bin/kubectl config use-context arn:aws:eks:us-west-2:647362336090:cluster/EKS-CXFL470QkkLM
					'''
				}
			}
		}

		stage('Deploy blue container') {
			steps {
				withAWS(region:'us-west-2', credentials:'ecr_credentials') {
					sh '''
						kubectl apply -f ./blue-controller.json
					'''
				}
			}
		}

		stage('Deploy green container') {
			steps {
				withAWS(region:'us-west-2', credentials:'ecr_credentials') {
					sh '''
						kubectl apply -f ./green-controller.json
					'''
				}
			}
		}

		stage('Create the service in the cluster to redirect to blue') {
			steps {
				withAWS(region:'us-west-2', credentials:'ecr_credentials') {
					sh '''
						kubectl apply -f ./blue-service.json
					'''
				}
			}
		}

		stage('Wait user approve') {
            steps {
                input "Ready to redirect traffic to green?"
            }
        }

		stage('Create the service in the cluster to redirect to green') {
			steps {
				withAWS(region:'us-west-2', credentials:'ecr_credentials') {
					sh '''
						kubectl apply -f ./green-service.json
					'''
				}
			}
		}

	}

}
