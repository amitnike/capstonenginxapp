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

		stage('docker setup') {
			steps {
				sh '''
					sudo groupadd docker
					sudo usermod -aG docker $USER
					sudo chmod 777 /var/run/docker.sock
				'''
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
						aws eks update-kubeconfig --name EKS-CXFL470QkkLM
						/home/ubuntu/bin/kubectl apply -f aws-auth-cm.yaml
						/home/ubuntu/bin/kubectl config use-context arn:aws:eks:us-west-2:647362336090:cluster/EKS-CXFL470QkkLM
					'''
				}
			}
		}

		stage('Deployment to AWS') {
			steps {
				withAWS(region:'us-west-2', credentials:'ecr_credentials') {
					sh '''
						/home/ubuntu/bin/kubectl set image deployments/capstone-app capstone-app=${registry}:latest
						/home/ubuntu/bin/kubectl apply -f capstone-app-deployment.yml
						/home/ubuntu/bin/kubectl get nodes
						/home/ubuntu/bin/kubectl get pods	
					'''
				}
			}
		}

	}
}