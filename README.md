# Kissflow

### Jenkins Installation

#### Add repository key to system

`wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
`
#### Add repository to sources

`sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
`

#### Update the repository

`sudo apt update`

#### Install Jenkins

`sudo apt install jenkins
`

#### Start Jenkins service

`sudo systemctl start jenkins
`

#### Access jenkins IP:8080 & Install neccessary plugins
  
#### Login az-cli and Create AKS Cluster 

`az login`
  
#### Create resource group
  
 `az group create --name demogroup --location eastus`
  
 #### Create AKS Cluster 
  
 `az aks create --resource-group demogroup --name clustername --node-count 1 --enable-addons monitoring --generate-ssh-keys`
  
#### Add Kubernetes config to access the cluster 
  
 `az aks get-credentials --resource-group demogroup --name clustername`
  
 #### Verify the nodes
  
 `kubectl get nodes`
  
