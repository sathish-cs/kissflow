pipeline {
	
	agent any
		

	
	stages {
		
		stage ('Nginx - Checkout') {
			steps {
					checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/sathish-cs/kissflow']]])
			}
		}
		stage ('Build, Lint, & Unit Test') {
			steps{
					//exectute build, linter, and test runner here    
					sh '''
					echo "exectute build, linter, and test runner here"
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
		stage ('Helm Deploy to K8s'){
			steps{
					sh '''
          #Docker Repo Config
					REPO_NAME="kissflow"
					ACR_LOGINSERVER="kissflow.azurecr.io"

  
					NAME="nginx"
					#HELM_CHART="./helm/"
					
					#Kubenetes config (for safety, in order to make sure it runs in the selected K8s context)
					KUBE_CONTEXT="kissflow"
					kubectl config --kubeconfig=/var/lib/jenkins/.kube/config view
					kubectl config set-context $KUBE_CONTEXT
					
				        sed -i "s/<TAG>/${BUILD_NUMBER}/" helm/templates/nginx.yaml
           				kubectl apply -f helm/templates/nginx.yaml
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
