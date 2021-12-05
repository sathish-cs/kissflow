pipeline {
	
	agent any
		

	
	stages {
		
		stage ('Nginx - Checkout') {
			steps {
					checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/sathish-cs/kissflow']]])
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

          #HELM config
					NAME="Nginx"
					HELM_CHART="./helm/"
					
					#Kubenetes config (for safety, in order to make sure it runs in the selected K8s context)
					KUBE_CONTEXT="kissflow"
					kubectl config --kubeconfig=/var/lib/jenkins/.kube/config view
					kubectl config set-context $KUBE_CONTEXT
					
					#Helm Deployment
					helm --kube-context $KUBE_CONTEXT upgrade --install --force $NAME $HELM_CHART --set image.repository=$ACR_LOGINSERVER/$REPO_NAME --set image.tag=jenkins${BUILD_NUMBER} 
					
					#If credentials are required for pulling docker image, supply the credentials to AKS by running the following:
					#kubectl create secret -n $NAME docker-registry regcred --docker-server=$ACR_LOGINSERVER --docker-username=$ACR_ID --docker-password=$ACR_PASSWORD --docker-email=myemail@contoso.com
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
