pipeline {
	
	agent any
		

	
	stages {
		
		stage ('Git - Checkout') {
			steps {
					checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/sathish-cs/kissflow']]])
			}
		}
		stage ('Build, Lint, & Unit Test') {
			steps{
					//This section can be utilzed to do test application before release. 
					sh '''
					echo "exectute build, linter, and test runner here". 
					'''
			}
	}
		stage ('Docker Build and Push to ACR'){
			steps{
					
					sh '''
					#Azure Container Registry config
					REPO_NAME="kissflow"
					ACR_LOGINSERVER="kissflow.azurecr.io"
					IMAGE_NAME="$ACR_LOGINSERVER/$REPO_NAME:jenkins${BUILD_NUMBER}"

					#Docker build and push to Azure Container Registry
					cd ./nginx
					docker build -t $IMAGE_NAME .
					cd ..
					docker push $IMAGE_NAME
					'''
			}
	}
		stage ('Deploy to K8s Cluster'){
			steps{
					sh '''
          #Docker Repo Config
					REPO_NAME="kissflow"
					ACR_LOGINSERVER="kissflow.azurecr.io"

					
					#Kubenetes config
					KUBE_CONTEXT="kissflow"
					kubectl config --kubeconfig=/var/lib/jenkins/.kube/config view
					kubectl config set-context $KUBE_CONTEXT
					
					# Replacing tag with jenkins build number
				        sed -i "s/<TAG>/${BUILD_NUMBER}/" nginx/nginx.yaml
					# Apply manifests to create/update deployment
           				kubectl apply -f nginx/nginx.yaml
					'''
				}
		}	
	}

	post { 
		always { 
			echo 'Build Steps Completed'
		}
	}
}
