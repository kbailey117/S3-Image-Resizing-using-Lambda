# Automatic Image Resizing and Transfer Using AWS S3 & Lambda
## Project Description:
This project focuses on building an automated system for image processing and management within the AWS ecosystem. The goal is to streamline the handling of images by automatically resizing them and transferring them to a designated storage location while keeping stakeholders informed through notifications. Key AWS services, such as Lambda, S3, and SNS, are used to orchestrate this workflow.

## Key Features:
1. Image processing automation: Automatically resize and optimize images upon upload.
2. Secure storage: Store processed images in a secure and reliable S3 bucket.
3. Real-time notifications: Receive immediate updates about image processing via SNS.
4. Scalable architecture: Design for scalability to handle image processing demands.
5. Cost-efficient solution: Leverage AWS serverless technologies to minimize operational costs.

## Overview:

<img width="1539" height="684" alt="overview" src="https://github.com/user-attachments/assets/d7cc06be-2177-4913-992d-4d8bae041165" />

## Steps:

### Step 1:
### Creating Source and Designation s3 Buckets:

1.	Navigate to the S3 Console.
2.	Create a unique S3 Bucket using a unique name.
3.	Enable Server-side encryption with Amazon S3 managed keys (SSE-S3) and ensure Bucket Key is set to enabled.
4.	Repeat the above process for the destination bucket using a unique name.

### Step 2:
### Creating the SNS Notification:

1.	Navigate to the SNS console.
2.	Create a new topic using Standard notification and set a unique name
3.	Do not alter any other settings and click on Create topic.
4.	Now under the Subscriptions tab, click Create subscription.
5.	Under Select protocol, choose the Email option.
6.	Enter your email address into the Endpoint field and click on Create subscription.

Note: You can use any other protocols also like SQS, HTTP, SMS etc.

### Step 3:
### Creating the Lambda:

1.	Navigate to the Lambda Console.
2.	Click on Create a function and use the Author from scratch option.
3.	Choose a unique name and under Runtime, select Python 3.9.
4.	Using the image-resizing-s3.py under the repository, replace the default code and deploy the changes.

### Note: Make sure you add the correct ARN name from your SNS notifications under line 13 to the code to ensure everything functions. 

5.	Next we need to give the appropriate IAM permissions for the Lambda function to work properly.

### Step 4:
### Creating the IAM Policy

1.	Follow the below images for easier navigation of the IAM setup:

<img width="1912" height="827" alt="iam1" src="https://github.com/user-attachments/assets/32ea414f-7528-4c43-b719-4366048f155e" />

<img width="1919" height="826" alt="iam2" src="https://github.com/user-attachments/assets/fcf4f0f1-8922-4dcb-b453-0e80f8119792" />

<img width="1920" height="825" alt="iam3" src="https://github.com/user-attachments/assets/000b24f0-8acc-496d-9b61-aed872889b5c" />

<img width="1920" height="825" alt="iam4" src="https://github.com/user-attachments/assets/50c10290-d7e1-4a4a-9fcb-c258495ef64b" />

<img width="1920" height="823" alt="iam5" src="https://github.com/user-attachments/assets/c697cf4d-adb8-47f3-92ff-dbdfe7936689" />

<img width="1920" height="843" alt="iam6" src="https://github.com/user-attachments/assets/7d3cbb05-75fb-43dd-93ff-665ecd7c1181" />

<img width="1919" height="826" alt="iam7" src="https://github.com/user-attachments/assets/49e994fa-8514-4729-8f6d-e0e7cff204cf" />

<img width="1920" height="825" alt="iam8" src="https://github.com/user-attachments/assets/56fb429f-6333-4ada-90b5-57d91bf4a958" />

2.	Now navigate to the Lambda Console and attach the policy to the Lambda function under Permission policies.
3.	Now we have to trigger the function.
4.	Click on Add trigger, find your S3 source bucket and select it.
5.	Under Event types, select All object create events.
a.	We use this trigger as whenever a new object is created in the S3 source bucket, it will trigger the Lambda automatically to resize and send to the destination bucket.
b.	manually add the Pillow library, however, it's very time consuming, so we will use Layers instead.

### Step 5:
### Adding Pillow to our Lambda function
### Note: This is important because in order to resize the image we upload in our source S3 bucket, a python library called Pillow is required in our code to resize the image.

1.	Now we have to go to the code section and scroll down to layers.
  a.	I already had Pillow installed locally, so I will make this easy on myself. There are many other ways to get Pillow uploaded to a Lambda function.
  b.	Create a Zip file of the Pillow directory using zip -r mylayer.zip python/
### Note: I have uploaded the mylayers.zip file under the Repo for those who just want to go faster!
  c.	Ensure the python directory is at the root of the zip file or Lambda will not recognize it.
3.	Under the Lambda function select a Add a layer.
4.	Select Upload a zip file and use the mylayer.zip that was created.
5.	Select the appropriate architecture and Python version 3.9 (same as was used above).

### Step 6:
### Testing our code before deployment

1.	After everything above has been completed we can test our code.
2.	The code should run successfully, however, it will return some errors due to no images actually being uploaded. Let’s test that next!

<img width="1724" height="528" alt="lambdatest1" src="https://github.com/user-attachments/assets/b52860ac-538b-42c9-ad60-4339b16ed1ad" />

### Step 7:
### Uploading an image into the S3 source to test full functionality:

1.	Navigate to the S3 Console.
2.	Upload an image into the bucket (you can use any, but in the Repo is a coffee.jpg for those who know!)
3.	Once uploaded into the source bucket, you can navigate to the destination bucket where you should see a new folder called resized/
4.	Inside this folder should be the compressed image file and you should also shortly receive an email notification from your SNS protocol!

