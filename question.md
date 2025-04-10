Exp 1: Working with EC2 Instance.

Task 1: Launch an EC2 Instance using Ubuntu OS Connect to the Instance using SSH and connecting it with ec2 direct connect 
Task 2: Install Nginx Server on Ec2 Instance Connect to the Nginx webpage using PublicIP
Task 3: Launch a New Instance using Amazon Linux Connect to the Instance using Putty
Task 4: Launch an Ec2 Instance using Windows OS Connect to the Instance using RDP 


Exp 2:  Deploy a front-end application using Docker.

Task 1: Install Docker on an EC2 instance.
Task 2: Clone the front-end application, write the Dockerfile build the image.
			Use : tinyurl.com/demokmit3
Task 3: Create a Docker container with a front-end application.
Task 4: Configure security groups to allow web traffic.


Exp 3: Create and configure storage services using Amazon EBS

Task 1: Launch an Ec2 Instance check the volume and modify the size of volume 
Task 2: Create a new Volume in the same availability zone and attach it to EC2 instance 
             -- Mount the new volume and
             -- Create a file (with your rollno.txt)
Task 3: Detach the new Volume from the EC2 Instance and try to attach it to a new EC2 Instance as well as check the files are present
Task 4: Create a snapshot, copy it across regions, and attach the new volume to an EC2 instance in the new region.


Exp 4:  Amazon Elastic File System

Task 1: Create Two Security Groups
           --- One for EC2
           --- One for EFS
Task 2: Launch One EC2 Instance and Mount EFS
Task 3: Create a directory and a file with your rollno under the directory
Task 4: Launch One more EC2 and mount the same EFS
Task 5: Access the Directory and File created from the First EC2 Instance 






Exp 5: Amazon VPC

Task 1: Creation of VPC
Task 2: Create Two Subnets (Public and Private) 
Task 3: Launch 2 EC2 Instances one in Public subnet and One in Private subnet
Task 4: Create Internet Gateway 
Task 5: Create Route Tables and associate IGW
Task 6: Access EC2 Launched in Private Subnet Via EC2 Launched in Public Subnet


Exp 6: Amazon VPC NAT Gateway

Task 1: Create VPC
Task 2: Create Two Subnets (Public and Private) 
Task 3: Launch 2 EC2 Instances one in public subnet and one in Private subnet
Task 4: Create Internet Gateway 
Task 5: Create Route Tables and associate IGW
Task 6: Create NAT Gateway
Task 7: Access Internet from EC2 Launched in Private Subnet


Exp 7: Create your First AWS S3 Bucket and Upload Content to Bucket and Manage their Access and Create Static Website using AWS S3

Task 1: Create an S3 bucket.
Task 2: Upload files (HTML, images).
Task 3: Set proper permissions.
Task 4: Enable static website hosting.


Exp 8: Setting up an AWS Lambda function to trigger whenever an object is uploaded to an S3 bucket and update a DynamoDB table

Task 1: Create a Lambda function.
			Use: tinyurl.com/kmitdemocode
Task 2: Set up API Gateway to invoke Lambda from the s3.
Task 3: Update the DynamoDB.
