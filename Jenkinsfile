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
			environment {
				tag_version = "${env.BUILD_ID}"
			}

			steps {
				withKubeConfig([credentialsId: 'kubeconfig']) {
					sh 'sed -i "s/{{TAG}}/$tag_version/g" ./k8s/kube-news.yml'
					sh 'kubectl apply -f ./k8s'
				}
			}
		}
	}

}