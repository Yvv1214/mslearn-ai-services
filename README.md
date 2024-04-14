# mslearn-ai-services
Lab files for Azure AI Services modules

# Get azure subscription & create an Azure AI services multi-service account 
- https://portal.azure.com
- on top row find AI services multi-service account
- on the top left click creat+ and fill in all required fields
- get the keys and endpoint of your AI Services account for your .env file
- run python filename.py to see the Text Analytics service in action

# Use SDK
-  pip install azure-ai-textanalytics==5.3.0

# Install AZ CLI
- https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli
- use the following command to get the list of Azure AI services keys, replacing <resourceName> with the name of your Azure AI services resource, and <resourceGroup> with the name of the resource group in which you created it.
-  az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
- it will return your keys
- you can use curl - a command line tool for HTTP requests. In the 02-ai-services-security folder, open rest-test.cmd and edit the command. Replace the endpoint and key with the ones you have.
- save it and run  sh rest-test.sh and the terminal will return a json doc stating the language it detected

# Ceate key vault
- in the Azure portals Home page, select the ＋Create a resource button and look for Key Vault, and create a Key Vault
- Give it the same resource group and region as your AI services account
- in the access config tab click on the permission model and pick vault access policy, then go down and pick the user thats gonna have access
- got to your key vault resources and select secrets and click on generate/import
- give it a name and a key value

# service principle
- u need a service principle to access the vault
- u create one using AZ CLI, <spName> is what u name it
- find ur subscription ID https://wmatthyssen.com/2019/09/06/azure-tip-how-to-quickly-find-your-azure-subscription-id-guid/
- az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
-  the output will include appId, password, and tenant values and u need to save those somewhere as they will only appear once!!!!!
-  now u need the object ID 
-  az ad sp show --id <appId>

# using service principle 
- cd Python/C-sharp and then run cd keyvault_client
- pip install azure-ai-textanalytics==5.3.0
- pip install azure-identity==1.5.0
- pip install azure-keyvault-secrets==4.2.0
- in the keyVaultClient folder is a configuration file and update the configurations it asks
- theres also a code file with a detectlanguage function
- run the code

# setup Alerts
- go to ur resources and select one
- in the left look for alerts and create one
- u can add metrics to a dashboard as well by creating a dashboard
- view your keys with az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
- go back to your alert and you will see a notification

# Visualize metrics
- go to your Azure AI services and click on metrics create one if needed
- In rest-test.cmd from Labfiles/03-monitor-ai-services input your endpoint and Key 1 in the Curl command then run  ./rest-test.cmd
- go back and to your metrics page and look at the chart

# Containers
- In Azure services find and create a contianer instances
- In Advanced Tab:
- Environment variables:
-  Yes	| ApiKey |	Either key for your Azure AI services resource
-  Yes	| Billing |	The endpoint URI for your Azure AI services resource
-  No	| Eula	accept

# Train classification Image model
- create new resource and find custom vision in the azure portal
- go to https://customvision.ai and create new project
- get 15 images for apple and upload and tag them and do the same for other fruits, u need to have atleast 2 tags used and each tag should be atleast in 5 images
- train the model and pick the quick training option
- do a quick test and see the metrics
- in the upper right click on settings and note the project and resourse for your project resource

# Use training API
- add the endpoint and key for your Custom Vision training resource, and the project ID in the .env in the train-classifier folder
- cd into the directory and pip install azure-cognitiveservices-vision-customvision==3.1.0
- python train-classifier.py
- return to customvision portal and refresh the page and see if ome new tagged images have been added to the project

# Use the image classifier from a client application
- 
