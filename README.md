# Azure Synapse End-End Demo

This repository provides one-click infrastructure and artifact deployment for Azure Synapse Analytics to get you started with Big Data Analytics on a 
large sized Health Care sample data. You will learn how to ingest, process and serve large volumes of data using various components of Synapse.

## Deploy Azure Synapse Demo in your azure environment

### Pre-requisites to deploy Synapse end-end demo

* You must have a github account
* You must have an active azure subscription

### Deployment Steps
Please follow below steps to successfully deploy Synapse workspace and its artifacts on your Azure Subscription

* Fork microsoft/AzureSynapseEndToEndDemo project to your local github account. Make sure to check "Copy the main branch only".

    ![Forking](/Images/Forking.gif)

* Once you fork AzureSynapseEndToEndDemo project to your github account, please click on **Deploy to Azure** button to start the deployment

    [![Deploy To Azure](/Images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2FAzureSynapseEndToEndDemo%2Fmain%2FARMTemplate%2Fazuredeploy.json)

* **Deploy to Azure** button takes you to https://ms.portal.azure.com/#create/Microsoft.Template webpage. Please provide subscription, resource group, region, storage account name, storage container name, workspace name, dedicated sql pool name, spark pool name, spark pool node size, sql administration username/password, sku (dedicated sql pool Data Warehouse Units) and github username parameter values.

    >*Note: The github username is should be target github account  where you forked the project to. Example: If https://github.com/microsoft/AzureSynapseEndToEndDemo is the github project url, then microsoft is github account name.*

* Click on **Review + Create** button to trigger deployment validation. If deployment validation is successful, the single click deployment will deploy a Synapse Workspace, dedicated sql pool, spark pool infrastructure. This deployment also enable git configuration all the required artifacts for the end-end demo committed to user github project. This completes the Azure Synapse end-end code deployment step

    >*Note: If deployment is incomplete, please look at the resource group activity log and find the latest deployment errors for more information*

### Demo Setup

First you will need to fill in some parameters and complete a few configuration steps before you can use the demos.  We need to provide the linked service to your storage account with the storage account name you chose during deployment.
![image](https://user-images.githubusercontent.com/59613090/192065803-c1c7ccd8-0ab5-487f-aeca-0bb957d9e24e.png)

Once you click on the linked service name it will open a panel where we can make changes and provide the correct parameters for our storage account.
![image](https://user-images.githubusercontent.com/59613090/192065892-d103a4b9-dffb-4198-8036-28ab4045382a.png)


### Demo Execution

TBD
