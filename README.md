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
  
  az group create --name Group --location eastus

  myResourceGroup = Group
  
  Replace eastus with your preferred location.
  
- Create an Azure Container Registry (ACR):
  
  az acr create --resource-group Group --name shulinzhaoregistry --sku Basic

  myResourceGroup = Group
  myregistry = shulinzhaoregistry 
  
  Replace myRegistry with a unique name for your ACR.

- Login to ACR: az acr login --name myregistry

  myregistry = shulinzhaoregistry

- Build and Push Images to ACR:
  
  You need to tag your images with the ACR login server name and push them.
  
  docker tag my-fuseki myregistry.azurecr.io/my-fuseki:v1
  
  docker tag my-sparklis myregistry.azurecr.io/my-sparklis:v1
  
  docker push myregistry.azurecr.io/my-fuseki:v1
  
  docker push myregistry.azurecr.io/my-sparklis:v1

  my-fuseki = my-project-fuseki

  my-sparklis = my-project-sparklis
  
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

  The --dns-name-label must be unique within the Azure region.
  ACR Credentials
  
  For ACR, you will need the username and password. You can enable the admin user on your ACR to get these credentials.
  
  Enable Admin User on ACR:
  
  Navigate to your Azure Container Registry in the Azure Portal.
  
  Select "Access keys" under the "Settings" section.
  
  You'll see the "Admin user" option. Enable it if it's not already.
  
  Retrieve Credentials:
  
  Once the admin user is enabled, you will see the "Username" and "Password" fields populated with the values you need.Copy the username and either one of the passwords provided.
  
  Storage Account Key
  
  For the storage account key, follow these steps:
  
  Navigate to Your Storage Account:
  Go to the Azure Portal.
  
  Open the storage account you created (in your case, zhaoshulinstorage)
  
  Access Keys:
  In the storage account's menu pane, under "Security + networking", find and select "Access keys".
  
  Here, you'll find two access keys. You can use either "key1" or "key2".
  
  Click "Show keys", and then click the "Copy" button next to the key you wish to use.

**3.	Now that you have both the Fuseki and Sparklis containers running in Azure Container Instances (ACI) and they are publicly accessible.**
- Link for Fuseki: my-fuseki-app.eastus.azurecontainer.io:3030
- Link for sparklis: my-sparklis-app.eastus.azurecontainer.io
- Endpoint link is http://my-fuseki-app.eastus.azurecontainer.io:3030/pizza/sparql, which need to place in the sparklis. We can replace "pizza" in this link with the database name we uploaded in Fuseli.
