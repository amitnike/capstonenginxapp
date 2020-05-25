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

		stage('Deploy Image') {
			steps{
				script {
					docker.withRegistry( '', registryCredential ) {
					dockerImage.push()
				}
				}
			}
		}

	}
}