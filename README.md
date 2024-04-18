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

# Facial recognition
- https://github.com/MicrosoftLearning/mslearn-ai-vision go to 04-face folder and cd into computer vision
-  pip install azure-ai-vision==0.15.1b1
-  add endpoint and key in the .env
-  in the code file there is a # import namespaces, add this below it: import azure.ai.vision as sdk
-  in detect_people.py there is a  # Authenticate Azure AI Vision client, add this below it: cv_client = sdk.VisionServiceOptions(ai_endpoint, ai_key)
-  in analyzeImage function there is a # Specify features to be retrieved (PEOPLE), add this below it:
```
 analysis_options = sdk.ImageAnalysisOptions()
    
 features = analysis_options.features = (
     sdk.ImageAnalysisFeature.PEOPLE
 )
```  
 
- then he comment Get image analysis:
```
   # Get image analysis
 image = sdk.VisionSource(image_file)
    
 image_analyzer = sdk.ImageAnalyzer(cv_client, image, analysis_options)
    
 result = image_analyzer.analyze()
    
 if result.reason == sdk.ImageAnalysisResultReason.ANALYZED:
     # Get people in the image
     if result.people is not None:
         print("\nPeople in image:")
        
         # Prepare image for drawing
         image = Image.open(image_file)
         fig = plt.figure(figsize=(image.width/100, image.height/100))
         plt.axis('off')
         draw = ImageDraw.Draw(image)
         color = 'cyan'
        
         for detected_people in result.people:
             # Draw object bounding box if confidence > 50%
             if detected_people.confidence > 0.5:
                 # Draw object bounding box
                 r = detected_people.bounding_box
                 bounding_box = ((r.x, r.y), (r.x + r.w, r.y + r.h))
                 draw.rectangle(bounding_box, outline=color, width=3)
            
                 # Return the confidence of the person detected
                 print(" {} (confidence: {:.2f}%)".format(detected_people.bounding_box, detected_people.confidence * 100))
                    
         # Save annotated image
         plt.imshow(image)
         plt.tight_layout(pad=0)
         outputfile = 'detected_people.jpg'
         fig.savefig(outputfile)
         print('  Results saved in', outputfile)
    
 else:
     error_details = sdk.ImageAnalysisErrorDetails.from_result(result)
     print(" Analysis failed.")
     print("   Error reason: {}".format(error_details.reason))
     print("   Error code: {}".format(error_details.error_code))
     print("   Error message: {}".format(error_details.message))
```
- run  python detect-people.py

# Use the face SDK
- CD into and open 04-face folder -> face-api
-  pip install azure-cognitiveservices-vision-face==0.6.0
-  in the code file  under # Import namespaces add:
```
 from azure.cognitiveservices.vision.face import FaceClient
 from azure.cognitiveservices.vision.face.models import FaceAttributeType
 from msrest.authentication import CognitiveServicesCredentials
```
- in the main function under //Authenticate Face client add
```
 credentials = CognitiveServicesCredentials(cog_key)
 face_client = FaceClient(cog_endpoint, credentials)
```

# Detect and analyze face
- in DetectFaces function under # Specify facial features to be retrieved add
```
features = [FaceAttributeType.occlusion,
             FaceAttributeType.blur,
             FaceAttributeType.glasses]
```
- then under # Get faces
```
with open(image_file, mode="rb") as image_data:
    detected_faces = face_client.face.detect_with_stream(image=image_data, return_face_attributes=features, return_face_id=False)

    if len(detected_faces) > 0:
        print(len(detected_faces), 'faces detected.')

        # Prepare image for drawing
        fig = plt.figure(figsize=(8, 6))
        plt.axis('off')
        image = Image.open(image_file)
        draw = ImageDraw.Draw(image)
        color = 'lightgreen'
        face_count = 0

        # Draw and annotate each face
        for face in detected_faces:

            # Get face properties
            face_count += 1
            print('\nFace number {}'.format(face_count))

            detected_attributes = face.face_attributes.as_dict()
            if 'blur' in detected_attributes:
                print(' - Blur:')
                for blur_name in detected_attributes['blur']:
                    print('   - {}: {}'.format(blur_name, detected_attributes['blur'][blur_name]))
                    
            if 'occlusion' in detected_attributes:
                print(' - Occlusion:')
                for occlusion_name in detected_attributes['occlusion']:
                    print('   - {}: {}'.format(occlusion_name, detected_attributes['occlusion'][occlusion_name]))

            if 'glasses' in detected_attributes:
                print(' - Glasses:{}'.format(detected_attributes['glasses']))

            # Draw and annotate face
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline=color, width=5)
            annotation = 'Face number {}'.format(face_count)
            plt.annotate(annotation,(r.left, r.top), backgroundcolor=color)

        # Save annotated image
        plt.imshow(image)
        outputfile = 'detected_faces.jpg'
        fig.savefig(outputfile)

        print('\nResults saved in', outputfile)
```
- run  python analyze-faces.py

# Read text in imgaes
- https://github.com/MicrosoftLearning/mslearn-ai-vision
- Labfiles\05-ocr -> read-text
-  pip install azure-ai-vision-imageanalysis==1.0.0b1
-  in the code file import:
```
 # import namespaces
 from azure.ai.vision.imageanalysis import ImageAnalysisClient
 from azure.ai.vision.imageanalysis.models import VisualFeatures
 from azure.core.credentials import AzureKeyCredential
```
- in the main function add
```
 # Authenticate Azure AI Vision client
 cv_client = ImageAnalysisClient(
     endpoint=ai_endpoint,
     credential=AzureKeyCredential(ai_key)
 )
```
- in the GetTextRead function add:
```
 # Use Analyze image function to read text in image
 result = cv_client.analyze(
     image_data=image_data,
     visual_features=[VisualFeatures.READ]
 )

 # Display the image and overlay it with the extracted text
 if result.read is not None:
     print("\nText:")

     # Prepare image for drawing
     image = Image.open(image_file)
     fig = plt.figure(figsize=(image.width/100, image.height/100))
     plt.axis('off')
     draw = ImageDraw.Draw(image)
     color = 'cyan'

     for line in result.read.blocks[0].lines:
         # Return the text detected in the image

            
     # Save image
     plt.imshow(image)
     plt.tight_layout(pad=0)
     outputfile = 'text.jpg'
     fig.savefig(outputfile)
     print('\n  Results saved in', outputfile)
```
- then add under the comment Return the text detected in the image:
```
 print(f"  {line.text}")    
    
 drawLinePolygon = True
    
 r = line.bounding_polygon
 bounding_polygon = ((r[0].x, r[0].y),(r[1].x, r[1].y),(r[2].x, r[2].y),(r[3].x, r[3].y))
    
 # Return the position bounding box around each line
    
    
 # Return each word detected in the image and the position bounding box around each word with the confidence level of each word
    
    
 # Draw line bounding polygon
 if drawLinePolygon:
     draw.polygon(bounding_polygon, outline=color, width=3)
```
- run  python read-text.py
- undet the comment # Return the position bounding box around each line add:
```
print("   Bounding Polygon: {}".format(bounding_polygon))
```
- run again
- add another set of code under the corresponding comment:
```
# Return each word detected in the image and the position bounding box around each word with the confidence level of each word
for word in line.words:
    r = word.bounding_polygon
    bounding_polygon = ((r[0].x, r[0].y),(r[1].x, r[1].y),(r[2].x, r[2].y),(r[3].x, r[3].y))
    print(f"    Word: '{word.text}', Bounding Polygon: {bounding_polygon}, Confidence: {word.confidence:.4f}")
    
    # Draw word bounding polygon
    drawLinePolygon = False
    draw.polygon(bounding_polygon, outline=color, width=3)
```
- run again

# Azure Video Indexer
- upoad a vid at https://www.videoindexer.ai
- In the Insights pane, select the View symbol, and in the list of insights, add Keywords and Sentiments to the pane.
- In the search box enter the one word that describes your video and see if its labeled

# Use Video Analyzer widgets
- In 06-video-indexer folder open analyze-video.html
- Then go to your uploaded video and then click </> Embed to see the iframe, set the widget into player and size it 560 x 315
- under <– Player widget goes here – > in the code file paste the iframe code
- Then on widget switch it back to insight and copy the code again and paste under <– Insights widget goes here – >  the difference will the iframe size
- save and right-click analyze-video.html and select Reveal in File Explorer
- open analyze-video.html in your browser and experiment with the widgets

# 
- 
