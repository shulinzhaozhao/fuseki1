Deployment Guide for Sparklis and Fuseki on Azure using Docker

**1. Prerequisites**
- Azure Subscription: Ensure you have an active Azure subscription. Create an account at Azure Portal if you do not have one.
- Azure CLI: Install the Azure CLI tool on your local machine. 
- Docker: Docker needs to be installed locally to handle container operations. Installation instructions are available on the Docker website.
- Azure Container Registry (ACR): Set up an ACR through the Azure portal or via the Azure CLI to store and manage your Docker images.

**2. Configuration and Setup**
- Use this project file to open a terminal and run: docker-compose build

- Use this project file to open a terminal and run: docker-compose up

- Login to Azure CLI: Open a terminal and run: az login
- 
  This will open a browser window where you can log in with your Azure credentials.
  
- Create a Resource Group: Azure uses Resource Groups to manage resources. Create one with
  
  az group create --name myResourceGroup --location eastus
  
  Replace eastus with your preferred location.
  
- Create an Azure Container Registry (ACR):
  
  az acr create --resource-group myResourceGroup --name myregistry --sku Basic
  
  Replace myRegistry with a unique name for your ACR.

- Login to ACR: az acr login --name myregistry

- Build and Push Images to ACR:
  
  You need to tag your images with the ACR login server name and push them.
  
  docker tag my-fuseki myregistry.azurecr.io/my-fuseki:v1
  
  docker tag my-sparklis myregistry.azurecr.io/my-sparklis:v1
  
  docker push myregistry.azurecr.io/my-fuseki:v1
  
  docker push myregistry.azurecr.io/my-sparklis:v1
  
  Note: You need to build your Docker images with the docker build command if you haven't already done so. my-fuseki and my-    sparklis are the image name in docker.
  
- Deploy to Azure Container Instances (ACI):
  
  You'll deploy each image as a separate container. Here's how you deploy Fuseki:
  
  az container create \
      --resource-group myResourceGroup \
      --name my-fuseki \ 
      --image myRegistry.azurecr.io/my-fuseki:v1 \ 
      --cpu 1 --memory 1 \  
      --registry-login-server myRegistry.azurecr.io \
      --registry-username <acr-username> \
      --registry-password <acr-password> \
      --dns-name-label my-fuseki-app \
      --ports 3030
 - And Sparklis:
   az container create \
      --resource-group myResourceGroup \
      --name my-sparklis \
      --image myRegistry.azurecr.io/my-sparklis:v1 \
      --cpu 1 --memory 1 \
      --registry-login-server myRegistry.azurecr.io \
      --registry-username <acr-username> \
      --registry-password <acr-password> \
      --dns-name-label my-sparklis-app \
      --ports 80
  Replace <acr-username> and <acr-password> with your ACR username and password.

  The --dns-name-label must be unique within the Azure region.

  My username is zhaozhao.
  
  My password is bHmZUyN6oQrPM/o1rYMN5zj4D9ZljjxLdFwzGR6vso+ACRAkBx8l.

**3.	Now that you have both the Fuseki and Sparklis containers running in Azure Container Instances (ACI) and they are publicly accessible.**
- Link for Fuseki: my-fuseki-app.eastus.azurecontainer.io:3030
- Link for sparklis: my-sparklis-app.eastus.azurecontainer.io
- Endpoint link is http://my-fuseki-app.eastus.azurecontainer.io:3030/pizza/sparql, which need to place in the sparklis. We can replace "pizza" in this link with the database name we uploaded in Fuseli.
