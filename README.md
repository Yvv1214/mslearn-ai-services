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


