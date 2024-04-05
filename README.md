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

