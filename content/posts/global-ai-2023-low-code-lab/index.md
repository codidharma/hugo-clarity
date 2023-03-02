---
title: "Global AI Bootcamp 2023 Low Code Lab With Logic Apps and Azure AI Services"
date: 2023-03-01T11:05:48+05:30
toc: true
codeMaxLines: 50
codeLineNumbers: true
usePageBundles: true
featureImage: "FeaturedImage.jpg"
thumbnail: "FeaturedImage.jpg"
shareImage: "FeaturedImage.jpg"
draft: false
categories:
  - Technology
tags:
  - Logic Apps
  - Azure AI
  - Low Code
  - No Code
---

Welcome to the Global AI Bootcamp 2023. This is a lab designed by me to demonstrate how easy it is to create a speed infraction ticketing system using Azure Logic Apps, Azure AI with writing minimal amount of code. 

## Lab Introduction

I have always been a fan of movies. When I think of fast cars, comedy, and swag, Rowan Atkinson's Johnny English driving a Rolls Royce or Aston Martin always comes to my mind. He always ends up speeding his vehicle and gets captured in the speed detecting camera and believe me his facial expressions are always hilarious. Have a look!

![Johnny English Driving](giphy.gif)

Though Johnny English destroys the camera with his awesome car fired missile, this gives us a real life example where we can use the power of serverless computing to process the captured images of the driving vehicles.

## Lab Problem Statement

The people of Abracadabra country are speed aficionados and some times in their zeal of enjoying the thrill of fast speed, they cross the legal speed limit laid down by the local government. Hence the Minister of Transport has decided to install speed triggered cameras at major intersections in all the major cities pan Abracadabra. The cameras will capture the images of the vehicles and upload the images to the cloud. The process is

1. The cameras upload captured images to the cloud storage as blobs along with all the details like the district where infraction occurred etc.
2. The in cloud system detects the registration number and notifies the registered owner of the speeding infractions.

## Lab Goals

This lab intents to help participants understand how easy it is to implement the AI infused Low Code No Code systems which are easy to maintain and scale.

## Lab Steps
The lab will focus on following points
1. Learn about reactive style of application programming
2. Create Azure Computer Vision API instance
3. Create a no code / low code logic apps workflow which processes images uploaded to the blob storage and sends a notification to culprit

## Solution
The solution that can be implemented for the problem contains following steps
1. The image captured by motion cameras is upload to a blob storage container
2. The image upload event triggers the logic app workflow
3. The workflow retrieves the contents of the uploaded image
4. The workflow invokes the computer vision API to extract the number using OCR
5. The workflow retrieves the driver registration information and sends notification
6. The workflow creates a ticket in the ticket database
7. In case of failure, the workflow moves the image from the uploaded container to the container where some one manually process the image

## Setting Up Prerequisites

Before following with this Lab, it is necessary to have below things set up

### A Computer
Access to a computer and internet is required to do the development and follow along

### Getting Azure Subscription

An acive azure subscription is required. A free trial account can be set up using [Azure Trial Account Link](https://azure.microsoft.com/en-in/free/)

### Creating Resource Group

You will need a resource group to club all the assets we build during the lab. Create a resource group called `rg-ai-blobal-btcmp-2k23-01`. Following are the steps to create a resource group.
1. On the left hand side, click on the hamburger menu and select Resource groups
![Left Menu Blade](rg/menublade.JPG)

2. Click on Create
![Create Resource Group](rg/createrg1.JPG)

3. Fill out the form and click on Review + Create
![Review Data Form](rg/createrg2.JPG)

4. Once the validation passes successfully, click on create.
![Create Resource Group](rg/createrg3.JPG)

### Registering Event Grid Provider

Before we begin using reactive programming to access uploaded blobs, we need to enable Event Grid Provider for the subscription. Following are the steps to achieve this.
1. On Home page, select Subscription. You can also search for it at the top search bar
![Locate Subscription](subscription/subscription1.JPG)

2. Select the subscription you are using for this lab
![Select Subscription](subscription/subscription2.JPG)
3. Under Setting section, select Resource Providers
![Select Resource Providers](subscription/subscription3.JPG)

4. Scroll down and locate `Microsoft.EventGrid`. Select it and click on Register at top
![Register Event Grid](subscription/subscription4.JPG)

5. Once done, you will get notification and `Microsoft.EventGrid` will be registered as a resource provider.
![Event Grid Registered](subscription/subscription5.JPG)




### Setting up Email Account To Send And Receive Email

You can use your existing gmail account or create a free gmail account to send the ticket notifications. [Create a Gmail account](https://support.google.com/mail/answer/56256?hl=en)

## Solution Implementation

### Creating Azure Cosmos DB

We will use the Azure Cosmos DB to store the fictional vehicle registration data. We will also use the same database to store the created tickets. The registration data and ticket data will be stored in different containers in the database. We will use the `district` as the partition key to partition the data properly.

1. Click on the hamburger menu icon on the left side and select Azure Cosmos DB.
![Select Azure Cosmos DB](cosmosdb/menublade.JPG)
2. Click on Create
![Initiate Creation Operation](cosmosdb/create1.JPG)
3. Select Azure Cosmos DB for NoSQL
![Select NoSQL](cosmosdb/Create2.JPG)
4. Fill out the basics form (Change the Account Name) and click on Review + Create
![Fill Basics Form](cosmosdb/create3.JPG)
5. Click on Create
![Create Azure Cosmos DB](cosmosdb/create4.JPG)
6. Once done you will get the notification
![Creation Completed](cosmosdb/create5.JPG)

### Creating Azure Cosmos DB Collections

#### Registration Container
1. Navigate to the Cosmos DB and select Data Explorer on Left Hand Side and click on New Container
  ![Create new Conatiner](cosmosdb/RegistrationContainer1.JPG)
2. Fill out the form as shown below an click on OK
  ![Form](cosmosdb/RegistrationContainer3JPG.JPG)
  ![Form](cosmosdb/RegistrationContainer2.JPG)

#### Tickets Container
1. Click on New Container
2. Fill out the form as shown below and click on OK
![Form](cosmosdb/RegistrationContainer4.JPG)


### Creating Computer Vision API Instance

We will need Computer Vision API to use the OCR to extract the registration number. Follow below steps to create a free instance of the computer vision API

1. Click on the hamburger icon and click on create a resource

![Select Create a Resource](computervision/create1.JPG)

2. Click on AI + Machine Learning and the locate computer vision and click on Create
![Select Computer Vision](computervision/create2.JPG)

3. Fill out the form as shown below. You may want to change the name under instance details. Click on Review + Create
![Fill out form](computervision/create3.JPG)

4. Once the validation passes, click on Create to create the instance.
![Create API Instance](computervision/Create4.JPG)

5. You will get a notification once done. Click on the Go to resource. You will be navigated to the instance
![Deployment Done](computervision/Creative5.JPG)

6. On the resource page, on the left pane, click on the Keys and Endpoints and the copy Key 1 and Endpoint. We will need it later on.
![Copy Key and Endpoint Details](computervision/Create6.JPG)


### Creating The Blob Storage

1. Click on the hamburger icon and click on create a resource

![Select Create a Resource](computervision/create1.JPG)

2. Click on Storage and select Storage Account and click on Create
![Select Storage Account](storageaccount/Create1.JPG)

3. Fill out the form as shown below. You will need to change the name. Click on Review. No need to change any other properties
![Fill out Form](storageaccount/Create2.JPG)

4. Click on Create to create the storage account instance
![Create Storage Account Instance](storageaccount/Create3.JPG)

5. Once the deployment is done, Click on Go to resource
![Deployment Done](storageaccount/Create4.JPG)

6. On the left hand side, click on the Access Keys and copy the blob storage name and key(you will have to click on show and then copy the key)
![Copy Blob Access Details](storageaccount/accessKeys.JPG)

#### Adding Container

1. On left hand side blade, select containers.
![Select Containers](storageaccount/Create5.JPG)

2. Click on Container to add a new container and fill out the form as shown and click on Create
![New Container](storageaccount/Create6.JPG)

3. Repeat step 2 to create another container called `manualops`
![New Container](storageaccount/Create7.JPG)


With this we are set to start building the workflow.

### Creating Logic Apps Workflow

We will use Logic Apps service to create an orchestration workflow which will read the contents of the uploaded blob and detect number and send an email notification for the infraction. Follow the steps given below

1. Click on the hamburger icon and click on create a resource

![Select Create a Resource](computervision/create1.JPG)

2. Select Integration and Find Logic Apps and click on Create
![Select Logic Apps](la/Create2.JPG)

3. Fill out the form as shown below and click on Review + Create
![Form](la/Create3.JPG)

4. Once the validation passes, click on Create to start the deployment
![Create instance](la/Create4.JPG)

5. Once Deployment is done, click on Go to resource
![Deployment Done](la/Create5.JPG)


### Progamming the Logic Apps

We will now add the logic to the created logic apps. Follow the steps given below

1. Once you go to the logic apps, the logic apps designer will be presented to you. Here you can select different templates that are available out of the box. We will select the `Blank logic app` template

![Select Blank logic app template](la/selectLaTemplate.JPG)

2. Now we will add the trigger to the logic app. As the name suggest, the trigger is what starts the logic app. In our case, our trigger is `When a blob is created` event. When this event occurs for the blob uploaded to the `uploads` container we created earlier, we want to start the processing. In Search box, search for `Event Grid`. Select the `When a resource event occurs`
![Selecting Trigger](la/selecttrigger.JPG)

3. You will now be asked to sign in. Use Defauly directory and click on sign in. Complete the sign in.
![Sign In](la/signineg.JPG)

4. Now we will configure the Logic app to react to the blob created event when the blob is uploaded to the `uploads` container. Configure the Event Grid Trigger as shown below.

![Configure Trigger](la/configureegtrigger.JPG)

5. Click on Save to save the logic app.

6. Now we will add the logic to get the url of the uploaded blob from which we will get information about the container and the name of the uploaded blob. If we observe, the general schema for a blob created event as raised by Azure is of following form

```JSON
{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}
```

7. In the search bar search for variables and select `Initialize variable` action

![Initialize Variable to Store Blob Path](la/searchvariables.JPG)

![Initiaize Variable to Store Blob Path 2](la/selectvariable.JPG)

8. We will now read the url from the blob created event sampled above and then use logic apps functions to get the value of the path. We will read the `url` property in the data object in the trigger body using following formula
```
triggerBody()?['data']?['url']
```
This will give us the url of the uploaded blob. From this we want to extractout `/testcontainer/new-file.txt`. So we use another function called `uriPath`. SO our final function will look like below.

```
uriPath(triggerBody()?['data']?['url'])
```
this will give us the required path of the blob.

Click in the Value box on the variable form. A window will pop up on the right hand side, select `expression` there and enter above formula there and click on ok. Save the logic app.

![Read Blob Path Formula](la/readblobPath.JPG)

9. We will now read the content of the blob using the Blob Storage related action. In the search box, search for `Azure Blob Storage` and select `Get Blob Content using Path(V2)`
![Search for Azure Blob Storage](la/selectblobactions.JPG)

10. We will now need to use the blob storage details that we captured earlier to configure the connection to the blob storage. Once you select action in previous Step, you will be presented following screen. Fill out with the necessary details as shown below. Click on Create to create the connection

![Create Blob Connection](la/createblobconn.JPG)

11. Configure the action as shown below and save the logic app.
![Configure Get Blob Content](la/configureBlobPathAction.JPG)

12. We will now pass the content of the uploaded blob to the OCR function of the Computer Vision API. In the seach box, search for 'Computer Vision' and select `Optical Character Recognition(OCR) to Text` action.

![Search and Select Computer Vision](la/selectcv.JPG)
![Select OCR to Text action](la/selectocrtotext.JPG)

13. Once you select the action, you will be asked to create a connection for the Computer Vision API. Here you will need the access details saved earlier while creating the Computer Vision API instance. Fill out the form as shown below and click on Create

![Configure Computer Vision API connection](la/configurecvconn.JPG)

14. Configure the action as shown below and save the logic app.
![Configure OCR to Text Action Properties](la/configureoctToTextProps.JPG)

15. The OCR if it detects words in the image will give a response like shown in sample below.
```JSON
{
  "text": "MHXXBD8877"
}
```
while a failure to detect will produce following message

```JSON
{
  "text": ""
}
```
16. Lets code out the failure path. On this path we will create a new blob in the `manualops` container so that some other system can pick up the image for manual processing and we will delete the original blob in `uploads` container

17. In the search bar, search for `Condition` and select Control. Then select `Condition`
![Select Control](la/selectcondition.JPG)
![Select Condition](la/selectcondition1.JPG)

18. We will check if the `text` property in the output of `Optical Character Recognition(OCR) to Text` is empty now. If it is empty, we will follow the path defined in Step 16
We will use the formula 
```
empty(body('Optical_Character_Recognition_(OCR)_to_Text')?['text'])

```
Configure the condition as shown in below steps and Save the logic app

![Step 1](la/configurecond1.JPG)
![Step 2](la/configurecond2.JPG)

19. On the true path, click on `Add an action`. In the search box, search for `Azure Blob Storage` and select `Create Blob V2` as shown below
![Select Create Blob V2](la/selectCreateBlob.JPG)

20. We will no need to extract the name of the blob to copy it to the destination container. We already have the path stored in variable `varBlobPath` earlier. We will now manipulate it to get the name of the blob. If you recall, the blob url is of format 
```URL
https://my-storage-account.blob.core.windows.net/<containerName>/new-file.txt
```
and the `varBlobPath` variable contains value `/<containerName>/new-file.txt`.

we will use following formula to read the blob name
```
substring(variables('varBlobPath'),add(lastIndexOf(variables('varBlobPath'), '/'), 1))
```
and 
```
triggerBody()?['data']?['url']
```
to read the url of the uploaded blob.

Configure the properties of the action as shown below and Save the logic app

![Step 1](la/createBlob1.JPG)

![Step 2](la/createBlob2.JPG)

![Step 3](la/createBlob3.JPG)

Finally it should look like below
![Create Blob Fully Configured](la/createBlobfinal.JPG)

21. Now we will delete the original blob in the uploads container.  Click on Add an Action. In the Search bar,search for `Azure Blob Storage` and select `Delete Blob V2` as shown below
![Select Delete Blob V2](la/selectdeleteblob.JPG)

22. Configure the action by filling the form as shown below and save the logic app

![Configure Delete Blob V2](la/configuredeleteBlob.JPG)

23. We will now code the postive path where the text is detected in image. We will do so on the false part of the condition.

24. On the False Part, Click on `Add an action`





