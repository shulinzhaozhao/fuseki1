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

  This will open a browser window where you can log in with your Azure credentials.
  
- Create a Resource Group: Azure uses Resource Groups to manage resources. Create one with
  
  az group create --name myResourceGroup --location australiaeast
  
  example: az group create --name projectGroup --location australiaeast
  
  Replace australiaeast with your preferred location and myResourceGroup.
  
- Create an Azure Container Registry (ACR):
  
  az acr create --resource-group myResourceGroup --name myregistry --sku Basic
  
  example: az acr create --resource-group projectGroup --name shulinprojectregistry --sku Basic
  
  Replace myRegistry with a unique name for your ACR and myResourceGroup with the name you created previously.

- Login to ACR:

  az acr login --name myregistry

  example: az acr login --name shulinprojectregistry

  Replace myregistry with the name you created previously.

- Build and Push Images to ACR:
  
  You need to tag your images with the ACR login server name and push them.
  
  docker tag my-fuseki myregistry.azurecr.io/my-fuseki:v1
  
  docker tag my-sparklis myregistry.azurecr.io/my-sparklis:v1
  
  docker push myregistry.azurecr.io/my-fuseki:v1
  
  docker push myregistry.azurecr.io/my-sparklis:v1

  example:

  docker tag my-project-fuseki shulinprojectregistry.azurecr.io/my-project-fuseki:v1
  
  docker tag my-project-sparklis shulinprojectregistry.azurecr.io/my-project-sparklis:v1
  
  docker push shulinprojectregistry.azurecr.io/my-project-fuseki:v1
  
  docker push shulinprojectregistry.azurecr.io/my-project-sparklis:v1
  
  Note: You need to build your Docker images with the docker build command if you haven't already done so. my-fuseki and my-sparklis are the image name in docker.

- The Azure Portal provides a user-friendly interface to manage all Azure resources. Here’s how to create an Azure File Share using the Azure Portal:

  Log in to the Azure Portal: Go to https://portal.azure.com and log in with your credentials.

  Navigate to Storage Accounts: Click on "Storage accounts" from the resources list or search for it in the portal search bar.

  Create a Storage Account (if you don’t have one):

  Click on "Create" or "+ Add".
  
  Fill in the required fields like subscription, resource group, storage account name, location, and performance (Standard or Premium).

  Select "Standard_LRS" for the replication as you intended earlier.

  Review and create the storage account.

  Create a File Share:

  Once your storage account is created and listed, click on it to open the storage account overview.

  Under the "Data storage" section, click on "File shares".

  Click "+ File share" to create a new file share.

  Name your file share and define the quota (if applicable).

  Click "Create" to provision the file share.

- Deploy to Azure Container Instances (ACI):
  
  You'll deploy each image as a separate container. Here's how you deploy Fuseki:
  
    az container create \
        --resource-group Group \
        --name fuseki \
        --image shulinzhaoregistry.azurecr.io/fuseki:v1 \
        --cpu 1 --memory 1 \
        --registry-login-server shulinzhaoregistry.azurecr.io \
        --registry-username <acr-username> \
        --registry-password <acr-password> \
        --dns-name-label my-fuseki-app-unique2024 \
        --ports 3030 \
        --azure-file-volume-account-name mystorageaccount \
        --azure-file-volume-account-key <storage-account-key> \
        --azure-file-volume-share-name myshare \
        --azure-file-volume-mount-path /path/in/container
    
  example:

    az container create \
      --resource-group projectGroup \
      --name fuseki \
      --image shulinprojectregistry.azurecr.io/my-project-fuseki:v1 \
      --cpu 1 --memory 1 \
      --registry-login-server shulinprojectregistry.azurecr.io \
      --registry-username shulinprojectregistry \
      --registry-password GmPvh2R5fkwf6ZC6LqX7FoqruRGkA9dPByVCS5io5Q+ACRBWgGRc \
      --dns-name-label fuseki-app-shulinproject \
      --ports 3030 \
      --azure-file-volume-account-name shulinzhaostorage \
      --azure-file-volume-account-key 0DOyhnczcjjaM7efDtYACYJkHinCeSN/1NYDuz/drgM5eyKUXL9Jz5OBpSHSrVT3oyB/fgdxImJE+ASt5MsCzA== \
      --azure-file-volume-share-name shulinzhaoshare \
      --azure-file-volume-mount-path /fuseki

   And Sparklis:
   
     az container create \
      --resource-group Group \
      --name my-sparklis \
      --image shulinzhaoregistry.azurecr.io/sparklis:v1 \
      --cpu 1 --memory 1 \
      --registry-login-server shulinzhaoregistry.azurecr.io \
      --registry-username <acr-username> \
      --registry-password <acr-password> \
      --dns-name-label my-sparklis-app-unique2024 \
      --ports 80 \
      --azure-file-volume-account-name mystorageaccount \
      --azure-file-volume-account-key <storage-account-key> \
      --azure-file-volume-share-name myshare \
      --azure-file-volume-mount-path /path/in/container

    example:

       az container create \
        --resource-group projectGroup \
        --name sparklis \
        --image shulinprojectregistry.azurecr.io/my-project-sparklis:v1 \
        --cpu 1 --memory 1 \
        --registry-login-server shulinprojectregistry.azurecr.io \
        --registry-username shulinprojectregistry \
        --registry-password GmPvh2R5fkwf6ZC6LqX7FoqruRGkA9dPByVCS5io5Q+ACRBWgGRc \
        --dns-name-label sparklis-app-shulinproject \
        --ports 80 \
        --azure-file-volume-account-name shulinzhaostorage \
        --azure-file-volume-account-key 0DOyhnczcjjaM7efDtYACYJkHinCeSN/1NYDuz/drgM5eyKUXL9Jz5OBpSHSrVT3oyB/fgdxImJE+ASt5MsCzA== \
        --azure-file-volume-share-name shulinzhaoshare \
        --azure-file-volume-mount-path /var/www/sparklis/webapp


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
  
  Open the storage account you created (in your case, shulinstorage)
  
  Access Keys:
  
  In the storage account's menu pane, under "Security + networking", find and select "Access keys".
  
  Here, you'll find two access keys. You can use either "key1" or "key2".
  
  Click "Show keys", and then click the "Copy" button next to the key you wish to use.

**3.	Now that you have both the Fuseki and Sparklis containers running in Azure Container Instances (ACI) and they are publicly accessible.**
- Link for Fuseki: fuseki-app-shulinproject.australiaeast.azurecontainer.io:3030

  You will be asked for username and password when you go to fukeki, the username and password is generated by azure and you can get it in fukeki log.
  
  in my case username = admin  password = j0UtRV6RF9QkDhW
  
- Link for sparklis: sparklis-app-shulinproject.australiaeast.azurecontainer.io:80
- Endpoint link is http://fuseki-app-shulinproject.australiaeast.azurecontainer.io:3030/pizza/sparql, which need to place in the sparklis. We can replace "pizza" in this link with the database name we uploaded in Fuseli.
