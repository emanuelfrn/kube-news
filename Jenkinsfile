pipeline {
	agent any

	stages {
		stage('Build Docker Image') {
			steps {
				script {
					dockerapp = docker.build("ghcr.io/emanuelfrn/kube-news:${env.BUILD_ID}", './src')
				}
			}
		}

		stage('Push Docker Image') {
			steps {
				script {
					docker.withRegistry('https://ghcr.io', 'ghcr') {
						dockerapp.push('latest')
						dockerapp.push(env.BUILD_ID)
					}
				}
			}
		}

		stage('Deploy Kubernetes') {
			steps {
				withKubeConfig([credentialsId: 'kubeconfig']) {
					sh 'kubectl apply -f ./k8s'
				}
			}
		}
	}

}