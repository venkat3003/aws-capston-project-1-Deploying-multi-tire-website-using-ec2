# aws-capston-project-1-Deploying-multi-tire-website-using-ec2
# Project: Deploy a Multi-tier website using EC2 
This repository contains AWS projects completed by me during My AWS Learning Journey.
![Aws](https://user-images.githubusercontent.com/111115490/206626352-aeab954e-41c5-47f1-9fc1-ba56e9df0b30.jpg)

# Overview of Steps
1.Create a VPC, subnets, internet gateway, and edit route tables.

2.Create an application load balancer for the web tier (Internet-facing) and application tiers.

3.Create the application and web tiers with EC2 Configure security groups so the web tier only accepts traffic from the ALB (Application Load Balancer), and the application tier only accepts traffic from the web tier security group.

4.Create the database tier using RDS (free tier).

5.Verify the web tier can be accessed from the Internet and that it can ping the application tier.


# Create a VPC

1. In the AWS Management Console, navigate to VPC → Create VPC →VPC Settings → Resources to create → VPC only (Project VPC), 
   IPV4 CIDR block → 20.20.0.0/16 → Create VPC
   
  <img width="662" alt="image" src="https://user-images.githubusercontent.com/111115490/206632717-9c18150c-ef69-4174-bb8d-561100ffb1a4.png">
  

2.Subnets → Create subnet → VPC → VPC ID → select the VPC created 
   Name each subnet and provide a CIDR for each.


<img width="747" alt="image" src="https://user-images.githubusercontent.com/111115490/206635099-7ce35d27-c0d7-4049-8d6a-0a3957a254db.png">
  To be able to contain each tier in its own subnet, I have created a set of 1 public and 3 private subnets for each Availability Zone.
  
  # Internet Gateway 
  
create and name to allow the VPC’s public subnets traffic to the internet.
    
 <img width="726" alt="image" src="https://user-images.githubusercontent.com/111115490/206637349-b5a1c52c-fa10-4a8b-aaf6-56fd2414e45e.png">
  
# NAT gateway
Select NAT gateway from the VPC Dashboard → Create NAT gateway
    
<img width="447" alt="image" src="https://user-images.githubusercontent.com/111115490/206639094-bf4d41e1-4427-4eae-8fd8-10f7503f088b.png">


<img width="441" alt="image" src="https://user-images.githubusercontent.com/111115490/206639212-1b86a2fb-8e2f-48e5-ae35-6c9f8a5210e5.png">


<img width="218" alt="image" src="https://user-images.githubusercontent.com/111115490/206639277-7e837dac-10f1-4a2b-a519-1e16d41bcc89.png">


<img width="586" alt="image" src="https://user-images.githubusercontent.com/111115490/206639390-77610744-459a-4dde-abc7-4a79999e184d.png">


# Route Tables
 
 Create two routing tables, one for the public subnets and one for the private subnets. Using the Routes tab →“Edit routes” for the public route table to make sure the route destinations are set to our CIDR block (20.20.0.0/16) and the Internet Gateway (0.0.0.0/0). For the private route table, the destinations should be set to our CIDR block (20.20.0.0/16) with the Target pointing to the NAT gateway we created.
 
 
<img width="746" alt="image" src="https://user-images.githubusercontent.com/111115490/206652969-09490772-45f1-410a-9210-5f4036dff2fe.png">
 
 # security groups 
   
Create new security group → Basic details → Create a Security group name and select the VPC.Inbound rules must be configured to allow any IPV4 traffic through ports 80 (http) → Create security group.
   
![image](https://user-images.githubusercontent.com/111115490/206658751-309072fa-5c38-4a69-8433-4c051f0b7bb4.png)



# Configure Launch Templates for the Web Tiers
 
 Web Tier: EC2 → Instances → Name (public Instance) → AMI → Ubuntu 20.04 → Instance type t2.micro.Create new key pair. In Network settings Click Edit
 VPC → Our Own VPC → Subnet → Public subnet1 → Firewall (security groups) select existing security group choose Our Own security group.
 
 <img width="506" alt="image" src="https://user-images.githubusercontent.com/111115490/206662874-6d6cddd6-b137-44d9-847c-142e3b7cc184.png">
 
 
 click Instances → Name (private Instance) → AMI → Ubuntu 20.04 → Instance type t2.micro.Create new key pair. In Network settings Click Edit
 VPC → Our Own VPC → Subnet → private subnet → Firewall (security groups) select existing security group choose Our Own security group.
 
 
 
# Target Group 

Basic configuration → target type Instances → Target group (name) → Protocol HTTP Port 80 → Project VPC then Click Next
   
<img width="476" alt="image" src="https://user-images.githubusercontent.com/111115490/206687902-69180a6c-f1e4-40b9-870e-40a9d495c5aa.png">
   
Then Register targets Select application (private) & port 80 → click target group
   
<img width="743" alt="image" src="https://user-images.githubusercontent.com/111115490/206688361-ce0a9de7-4b08-4479-9f8a-e4e09b672673.png">


<img width="713" alt="image" src="https://user-images.githubusercontent.com/111115490/206688646-bcb2f390-9765-4d23-a60b-568630e5824b.png">


# Application Load Balancer

Basic Configuration → Load balancer name , Scheme →select “Internet-facing”, IP address type → select IPV4
 
 
Network mapping → VPC (custome VPC), Mappings → select each AZ and the public subnets associated with each
   
Listeners and routing select custome target group 
   
<img width="684" alt="image" src="https://user-images.githubusercontent.com/111115490/206690277-8b95d385-df75-475d-bb29-4bc8d3235aac.png">
   
# create Load balancer
   
<img width="735" alt="image" src="https://user-images.githubusercontent.com/111115490/206690571-205c0c88-d995-4bd0-b4e4-b7aec565b3ac.png">
   
# creating RDS Database 
   
click database → Choose a database creation method as Standard create → Engine options
     
<img width="551" alt="image" src="https://user-images.githubusercontent.com/111115490/206693045-57d45e5f-a130-4d3e-becc-f6485599a414.png">

     
Click Templates Free tier

<img width="508" alt="image" src="https://user-images.githubusercontent.com/111115490/206693120-cd2a9c6d-30d4-479c-9044-471969e14459.png">
     
Settings → Give DB instance identifier as Project 

<img width="455" alt="image" src="https://user-images.githubusercontent.com/111115490/206694036-884c85a7-df57-43a1-a9d2-6d60334fce58.png">
   
In Connectivit Compute resource as Default → VPC choose custome Vpc → DB Subnet group choose Existing VPC security groups and click creat database
   
<img width="421" alt="image" src="https://user-images.githubusercontent.com/111115490/206695522-8fd751a2-0987-4865-97e4-23bbdebf0ae2.png">
    
    
# Creating S3 Bucket

> In General configuration → Bucket name (custome)
 
<img width="464" alt="image" src="https://user-images.githubusercontent.com/111115490/206696799-0d0b6b01-abf3-46e8-b70c-f405c609500a.png">
 
> Object Ownership → ACLs disabled 
 
<img width="521" alt="image" src="https://user-images.githubusercontent.com/111115490/206696951-3460b299-f8cc-4c50-8906-8992768c1764.png">
 
> click create bucket

<img width="710" alt="image" src="https://user-images.githubusercontent.com/111115490/206697335-6c875d59-170c-46a8-b7f1-6d0010157bca.png">

# Creating DynamoDB

DynamoDB → tables → Create table → Give table name and Partition key

<img width="461" alt="image" src="https://user-images.githubusercontent.com/111115490/206699152-00678d8d-5e0c-463c-975e-7b720bab6b22.png">

> Click create table

<img width="740" alt="image" src="https://user-images.githubusercontent.com/111115490/206699305-abe5f78a-5ae7-4950-ab81-0a5867cf0929.png">

# Connecting Ec2 Instance Using Putty
   
sudo apt-get update
sudo nano key.pem
ssh -i newjenkins.pem ubuntu@private Key( application)

<img width="482" alt="image" src="https://user-images.githubusercontent.com/111115490/206691887-f0fc7635-aff4-4772-9e78-92690ed2a47c.png">

sudo nano key.pem
sudo chmod 400 newjenkins.pem
ssh -i key.pem ubuntu@private ip Address
sudo apt get install mysql-client
   
<img width="836" alt="image" src="https://user-images.githubusercontent.com/111115490/206704572-7b570dd4-a794-4b6a-90d2-68ef1fa1667d.png">
   
sudo apt-get update

<img width="799" alt="image" src="https://user-images.githubusercontent.com/111115490/206705006-5fbde59f-faaa-4c97-a7f4-1028ea33136d.png">

# show databases;

<img width="725" alt="image" src="https://user-images.githubusercontent.com/111115490/206705382-9fb5ce31-ce64-46ec-aea7-e3b0969548e0.png">

# use Guru
Database changed

mysql> CREATE TABLE employee (emp_id VARCHAR(20), first_name VARCHAR(20),last_name VARCHAR(20),primary_skills VARCHAR(20), location VARCHAR(20));

<img width="957" alt="image" src="https://user-images.githubusercontent.com/111115490/206707735-6df1f9e7-7920-4e49-aa18-97a8e565a818.png">

# mysql> describe employee;

<img width="470" alt="image" src="https://user-images.githubusercontent.com/111115490/206707870-7cc84bd7-932c-41e1-9d3e-36f0b09e4694.png">

mysql> exit

sudo apt-get install git

<img width="563" alt="image" src="https://user-images.githubusercontent.com/111115490/206708927-2ff8b68c-4a91-4cbd-8623-f8046bb8e029.png">

 git clone https://github.com/guru716/aws-code-main.git
 
 <img width="583" alt="image" src="https://user-images.githubusercontent.com/111115490/206709041-892c9879-a674-42be-ac6e-793157007ace.png">

> ls

> aws-code-main

> cd aws-code-main

> ls

<img width="503" alt="image" src="https://user-images.githubusercontent.com/111115490/206711801-9c268913-9c48-4fa0-978f-18763de1cbf0.png">

sudo nano config.py

<img width="509" alt="image" src="https://user-images.githubusercontent.com/111115490/206712092-be605ebd-38aa-4897-8f17-a145f6877043.png">

sudo apt-get install python3

<img width="522" alt="image" src="https://user-images.githubusercontent.com/111115490/206712229-25581032-a1f1-43ef-95d4-2d67b5fb75b2.png">

sudo apt-get install python3-pymysql

<img width="835" alt="image" src="https://user-images.githubusercontent.com/111115490/206712421-0c691f95-954e-4ded-8a12-d50fa33bccbc.png">

sudo apt-get install python3-flask

<img width="899" alt="image" src="https://user-images.githubusercontent.com/111115490/206712553-13b224f7-a45e-4264-bbe7-fb80dda69628.png">

sudo apt-get install python3-boto3

<img width="929" alt="image" src="https://user-images.githubusercontent.com/111115490/206712685-f8f5d044-f2c2-48cc-9e17-4269e4acea1f.png">

ls
sudo python3 EmpApp.py

<img width="614" alt="image" src="https://user-images.githubusercontent.com/111115490/206712871-0ffc1ed3-978b-41a1-b5a7-06477730939b.png">

# end result

![image](https://user-images.githubusercontent.com/110183786/208388291-bc5d6183-5071-41dd-bdd7-8b1a38413e54.png)

![image](https://user-images.githubusercontent.com/110183786/208388693-b101e232-af8c-45b8-8efa-fa12d6cb4a29.png)



 
 

 

   
