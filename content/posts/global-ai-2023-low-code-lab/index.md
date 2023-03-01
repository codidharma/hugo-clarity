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

### Azure Subscription

An acive azure subscription is required. A free trial account can be set up using [Azure Trial Account Link](https://azure.microsoft.com/en-in/free/)

### Resource Group

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




### Email Account To Send And Receive Email

You can use your existing gmail account or create a free gmail account to send the ticket notifications. [Create a Gmail account](https://support.google.com/mail/answer/56256?hl=en)


