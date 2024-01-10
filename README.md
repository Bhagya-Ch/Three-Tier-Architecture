# Three-Tier-Architecture
Building a Resilient Three-Tier Architecture on AWS with Deploying MERN Stack Application



I will show you how we can build Resilient Three-Tier Architecture Deploying the MERN Stack app on AWS: Achieving High Scalability, high availability, and Fault Tolerance. In this project we are going to use multi-region deployment, one will be the primary region and the second will be for disaster recovery.

Three-tier architecture is a software architecture pattern that separates an application into three layers.

🔸Presentation layer ➡ handles user interaction

🔸Application layer(backend logic) ➡ processes business logic and data processing

🔸Data layer (database) ➡ manages data storage and retrieval
 Architecture of the Project:






Step-by-step guide:
We are following a Warm standby Disaster recovery strategy so we are going to utilize two regions during our deployment. us-east-1 AKA North Virginia as primary and us-west-2 AKA Oregon as secondary or DR.

1)VPC:

Firstly, we are going to set up VPC in both regions t o isolate our resources from the internet. 
Please enter the name that you want to keep and the IPV4 CIDR block.
VPC : IP range 172:20.0.0/16

**Subnet:

Availability Zone: us-east-1a/ us-west-2b

We are going to create a total of 8 subnets of which 2 of them are public and the rest of 6 subnets are private.

1)please log in to your AWS Account and type VPC in the AWS console. and click on VPC service.
2)click on Your VPC's button on the left and then click on Create VPC the button on the top right corner of the page, here we can see the form where we can fill the configuration of VPC. please enter the name that you want to keep and the IPV4 CIDR block. in my case CIDE block is 172.20.0.0/16.
3)Now click on the subnet button which is located on the left side and then click on theCreate subnet button on the top right corner of the page.
4)please remove the default VPC ID and choose the VPC ID that we have just created in the VPC ID field. and click on the Add Subnet button at the bottom.
5)We are going to create a total of 8 subnets of which 2 of them are public and the rest of 6 subnets are private. you can create a subnet as I have shown in the below image. after adding all the subnets click on Create subnet button.

6)After the successful creation of all 8 subnets, they look like this. you can verify with my subnets.***image


 2)IGW:

1)Now we are going to create an Internet Gateway also known as IGW.  It is responsible for communication between VPC, VPC's public subnet with the Internet. without IGW  we won't be able to communicate with the Internet. So let's create that. click on the  internet gateways button at the left panel. and then click on the Create Internet gateways button on the top right corner of the page.
2)Give any name you wanna give to IGW. and click on Create Internet gateway button.
3)After creating an internet gateway we need to attach it with a VPC to use it.  for that click on the Action button. here you can see the drop-down list. Please select the option Attach to VPC.
4)Please select the VPC that we have created just now from the Available VPC list. and then click on the Attach Internet gateway button.

3)NAT Gateway:
1)Now we need to create a NAT gateway. NAT gateway is responsible to connect resources that are in the private subnet to communicate with the internet. all the resources which will be there in a private subnet will communicate to the internet through the NAT gateway.
2)We will keep the NAT gateway in the public subnet so that it can access the internet. 
3)Now to create a NAT gateway click on the NAT gateways button on the left panel of the web page. and then click on the Create NAT gateways button in the top right corner of the page.
4)give any name you want to give to the NAT gateway. but be cautious with selecting a subnet. You have to select one of the Public subnets among the two. either pub-sub-1a or pub-sub-2b. then click on the Allocate Elastic IP button to allocate Elastic IP. and then click on the Create NAT gateways button. NAT gateways creation takes 2-4 minutes. 


4)Route table:
1)Now we need to have a route table to handle traffic for public subnet and private subnet and for that, we need to create a Route table. 
2)We are going to create two route tables, one for the public subnet and another one for the private subnet. 
3)First, we are going to create RT for the public subnet. so click on the Route table button which you can see on the left panel. and click on the Create Route table button on the top corner of the page.
4)Give a name to your RT such as Pub-RT. please give a name that is appropriate for resources then it will be easy to organize the things. make sure you select the correct VPC. and then click on the create route table.
5)Now create RT for a private subnet.Now, we need to do some association with both RTs so select Pub-RT and click on the Routes tab at the bottom and then click on the edit route button.
6)click on the Add Route button. and select 0.0.0.0/0 in the destination field. and then click on the Target field. As soon as you click on the Target field one drop-down will open and here you have to select Internet gateway
7)here you can see the IGW that we created earlier. select that IGW and click the save changes button.
8)keep Pub-RT selected and click on the Subnet associations tab next to the Routes tab. and then click on the Edit subnet associations.
9)now select both public subnets. pub-sub-1a and pub-sub-2b.  and click on the save associations button.
10)Now we have to do the same thing for the Pri-RT as well. but there is one slight change. 
Please select Pri-RT and click on the Routes tab at the bottom of the page.
11)Here please select 0.0.0.0/0 in the destination field and click on the target. As soon as you click on the target you will see the drop-down list. Please select NAT gateway from the drop-down list. select the NAT gateway that we have just created. and click on the save changes button.
12)keep Pri-RT selected and click on the subnet associations tab at the bottom next to the Routes tab. And then click on the Edit route associations button.
13)Here you can see the same situation as we saw before. But here we are going to select all the 6 private subnets. And then click on the save association button.



5)VPC & public subnet:

1)Go to VPC,change the settings of VPC. So just click on the Your VPC button on the left panel and select the VPC that we have created and click on the action button and there you will see the drop-down menu. Select the Edit VPC setting button.
2)And here please enable the Enable DNS hostname checkbox by clicking on it. and then click on the Save button
3)go to the subnet and select the public subnet and click on the action button and then choose the Edit subnet setting button from the drop-down list.
4)Here you have to mark right on Enable public assign public IPV4 address. And then click on the save button
5)And here we are done with VPC configuration in the primary region. In my case us-east-1 (N.Virginia). But we have to do the same setup in the secondary region as well. As you know I am going to use the us-west-2 (Oregon) as my second region AKA Oregon. 
I am not mentioning steps for other regions here, as all set up is completely similar.

6)Security Groups (SG):
Security groups are a very essential part of the infrastructure. Because it can secure the resources in the cloud. SGs are a kind of firewall that allow or block incoming and outgoing traffic. SGs are applied to the resources like ALB, ec2, rds, etc. One resource can have more than one SG.

1)To create SG, click on the security groups tab on the left panel and here you will see the Security Groups button.
2)When you create SG please make sure that you choose the right VPC. click on the crate security button on the top right corner.
3)We will create our first SG for bastion-jump-servers. Give any name and description you want but please remove the default VPC  and add VPC that we have just created. Then click on the Add rule button in inbound rules. And add the SSH rule and add your IP in the destination. Please don’t do anything with the outbound rule if you don't have a good understanding. And then click on the create security group button.
4)Now let's create SG for the ALB-frontend. Again steps are similar but add the rule HTTP AND HTTPS from anywhere on the internet because both ALB are internet facing. But please select the right VPC.
5)Create SG for the ALB-backend. ALB-backend is also internet-facing. Again allow HTTP and HTTPS from anywhere.
6)create SG for frontend servers. Our front end server will be in a private subnet so add the HTTP rule and select the source as ALB-frontend-sg.  So only the ALB-frontend can access the frontend server on port 80. You have to add one more rule SSH allows from bastion-jump-server-sg. So that the bastion host can log in to web servers.
7)Let's create SG for the backend server. Again steps are completely similar to frontend-sg. You have to allow port 80 from ALB-backend-sg so that only ALB-backend can request to the backend server and add the rule SSH allows from bastion-jump-server-sg. So that the bastion host can log in to backend servers.
8)Lastly, we are going to create an SG for RDS instance. Allow port 3306 MySql/Arrora from backend-sg so that only the backend server will be able to access it. and no one else can access our database.And here our SG setups are complete now. We need to do the same for Oregon(us west-2).


7) RDS and Route 53:
As I have restrictions for cost I didn't create any RDS due to cost issues but mentioning steps here.

 subnet group:
1)to the RDS dashboard. Just search RDS in the AWS console.
2)Now first we need to set up a subnet group. It specifies in which subnet and Availability zone out database instance will be created. So click on the subnet group button on the left panel. And click on the button Create database subnet group which is in the middle of the web page.
3)Here we can configure our VPC, subnet, and availability zone.  Give any name to your subnet but make sure you select the correct VPC. and select Azs us-east-1a and us-east-2b. According to the architecture that I have shown you, our database will be in private subnet  pri-sub-7a and pri-sub-8b. so please select as I have shown in the below figure. And then click on the create button.
4)We need to create a subnet group in the Oregon region as well.  All the configuration is similar to the above.  just need to change the Availability zone us-west-2a and us-west-2b.

8)Database:

5)Now come to the N.virginia region and here we are going to create a database. So click on the database button on the left panel and then click on the created database button.
6)Select stander create,select MySQL in the engine option because our application runs on MySQL database. If your app runs on other engines you can select that one.
7)Scroll down, and select Dev/test as  template. If you select the free tier then you won’t be able to deploy RDS in a multi-availability zone. 
8) Select Multi-AZ DB instance from availability and durability option. In settings give any name to your database. 
9) In the credential setting give the username of the database in the Master username field and give the password in the Master password field. And then confirm the password below. Please do remember your username and password.
10) select Brustable class in the instance setting and select the instance type.
11)I am selecting t3.micro.  now in storage type select General purpose(GP2) and allocate 22 GiB for database. Please uncheck the auto-scaling option to keep our costs low.
12)In connectivity select "Don't connect to an EC2 compute resource and network type IPv4.
13)In VPC, select VPC that we created earlier and in DB subnet group select the group that we just created, In the public access option please select No, choose existing security, and select security group book-rds-db.
14) In the database option give the name any name you want.Enable Automated Backup. 
15)Scroll down, mark on enable encryption checkbox to make the database bit more secure, and click on Create database button below.
16)After your database is completely ready and you see the status Available then select the database and click on the Action button. There you can see the drop-down list. Please click on created read-replica in other region.


9)Route 53:

Now we are going to utilize route 53 service and create two private hosted zone. One for north Virginia(us-east-1) and another one for Oregon region (us-west-2)  with the same name. 

1)Firstly, we are gonna create a hosted zone for us-east-1. Click on the Hosted Zones button on the left panel and click on the created hosted zone button on the top right corner.
2)Give any domain name because anyhow it will be private hosted zone.Please select the private hosted zone and Select the region. In my case, it is us-east-1. And then select VPC ID. Make sure you select VPC that we created earlier. Because this hosted zone will resolve the record only in specified VPC.  and then click on the Create hosted zone.
3)Now we are going to create a Record that points to our RDS instance which is in us-east-1. So click on create record button on the top right corner.Select simple routing,Click on the defined record button in the middle of the box.
4)Here type name(book) in the record name field. In the record type select CNAME. In the value field paste endpoint of the RDS which is in us-east-1. Then click on the defined record button.Click on create record button.
5)Now we are going to create a new hosted zone with the same name. but for disaster recovery region and that is us-west-2 (Oregon).  While creating hosted zone please keep in mind that you need to choose the us-west-2 region and select VPC that you have created in the the us-west-2 region. with similar above steps.
6)Our next step is to set up a simple record that points to the read replica (database) which is in the us-west-2 (Oregon). So select the hosted zone that was created for us-west-2 and defined a simple record in that.  Everything is the same as we defined the record in the us-east-1 hosted zone.



 10)Certificate Manager:
 we are going to create certificates in both regions us-east-1 and us-west-2.
1)So let's head over to ACM (AWS certificate manager). Type certificate manager in the AWS console search bar. And click on the service.
2)Now click on the list certificates button on the left panel and then click on the request certificate on the top right corner.
3)Select the option Request the public certificate and click on the next button.
4)In the domain name field please type *.Your_Domain_Name.xyz
5)In the validation method select DNS 	validation and click on the request certificate.
6)Now we need to add a CNAME record in our domain. If you are not using route 53 then you need to add this CNAME record manually by going to your DOMAIN REREGISTER. And if you are using route 53 then click on the button create record in route 53 and click on the create record button. 
7)we created  certificate in N.virginia(us-east-1) but you need to do the same thing for the Oregon region( us-west-2 ).


11)Application Load balancer(ALB) and Route 53:

 We need two load balancers, one point to the backend server, and another point to the frontend server.


1)Type ec2 in the AWS console. and click on the EC2 service.
2)Click the target group button on the bottom of the left panel. And click on the create target group button in the middle of the page.
3)Here we can configure our TG. Select the instance in the target type. You can give any name to TG but try to give some relevant name such as ALB-frontend-TG because we are creating TG for ALB-frontend. In the VPC section select VPC that we created earlier.
4)Click on the create target group button.
5) now create TG for ALB-backend. Click on the create target group button. Select the target type Instance. Again give some meaning full name such as ALB-backend-TG. Select VPC that we have created.And click on the creatd target group.
6)So we have two TG. ALB-frontend-TG and ALB-backend-TG.Now let's associate these TG with the load balancer.


12)Load Balancer:
 So click on the Load Balancer button at the bottom of the left panel and click on the create load balancer button. First, we will create ALB for frontend.
7)Choose Application load balancer and click on create button.
8)here we can configure our ALB. First, give the relevant name to ALB such as ALB-frontend. Select the internet-facing option. In Network mapping select VPC that we have created. Select both availability zone us-east-1a and us-east-2b. and select subnet pub-sub-1a and pub-sub-2b respectively.
9)Select security group ALB-frontend-sg.  This SG we have created for ALB-frontend. In the listener part select TG that we have just created ALB-frontend-TG.
10)Scroll down and click on the create load balancer button.


Scroll down and click on the create load balancer button.
11)Select Internet facing option. And select VPC that we have created.
12)Select both availability zone us-east-1a and us-east-2b.  and select subnet pub-sub-1a and pub-sub2b. select security group ALB-backend-sg that we created for ALB-backend. And in the listner part select TG that we just created ALB-backend-TG.
13)Scroll down as click on the Created Load balancer button.

14)Now we have two load balancers, ALB-frontend and ALB-backend. But we need to add one more listener in ALB-backend. So click on ALB-backend.
15)Click on add listener the button that is located on the right side.
16)Here In listener details select HTTPS. Default Action should be Forward and select ALB-backend-TG. Now we need to select the certificate that we have created. So in the Secure Listener setting select the certificate.  And click on the add button below.
17)So here we successfully completed the ALB setup for the N.virginia region (us-east-1), and your task is to set up the same ALB for the Oregon region (us-west-2).


13) EC2:
A)
Now we are going to create a temporary frontend and backend server to do all the required setup, take snapshots and create Machine images from it.
1)Launch Instance button on the top right corner.
2)First, we are going to set up a frontend server. Give a name to your instance (temp-frontend-server). Select Ubuntu as the operating system. Choose the instance type as t2.micro.  click on Create key pair if you don’t have it.
3)keep the Network setting as it is. In the firewall setting select all the fields as I shown in the below image to keep things simple. And lastly, click on the Advance details option.
4)below in USER DATA Here in this text box, you can write your bash script file and that will be executed during the launch of the instance.

{
#!/bin/bash

sudo apt update -y

sudo apt install apache2 -y

curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\
sudo apt-get install -y nodejs -y

sudo apt update -y

sudo npm install -g corepack -y

corepack enable

corepack prepare yarn@stable --activate --yes

sudo yarn global add pm2

}

we have successfully launched temp-frontend-server.

B)
1)let’s launch a temporary backend server.  give a name to your instance (temp-backend-server). 
2) select ubuntu as the operating system. And select t2.mirco as instance type. Here we don’t have to create a new key, we can utilize the previous key that we have created while launching the frontend instance.
3)In the network setting, we don’t have to change anything just select whatever I have shown below image to keep things simple. And lastly please click on the advance details option.
4)Scroll down to the bottom of the page, and copy the bash script that I have given below. and paste it in the USER-DATA text box. This bash scripting installs some packages so that we don’t have to install them manually. And click on the launch instance.

{
sudo apt update -y

curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\
sudo apt-get install -y nodejs -y

sudo apt update -y

sudo npm install -g corepack -y

corepack enable

corepack prepare yarn@stable --activate --yes

sudo yarn global add pm2

}

Select temp-frontend-server. and copy the IP address of the instance. Now open Gitbash where you have downloaded your YOUR_KEY.pem file. And type the command.

ssh -i <name_of_key>.pem ubuntu@<Public_IP_add_of_Instance>


It will prompt you for your permission just type yes
Now you are successfully logged your remote temp-frontend-server. now our first task is to clone my git repo.

Before installation you need :

1)node.js 18.16.0+ installation:


  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh 

Activate nvm by typing the following at the command line.
  ~/.nvm/nvm.sh

Use nvm to install the latest LTS version of Node.js by typing the following at the command line.
 nvm install --lts

Test that Node.js is installed and running correctly by typing the following at the command line.
 node -e "console.log('Running Node.js ' + process.version)"

2)mongoDB 6.0.2+ installation:

{
sudo vi /etc/yum.repos.d/mongodb-org-4.4.repo
Then paste this content into that file

[mongodb-org-4.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/amazon/2/mongodb-org/4.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc

-After that, run this

sudo yum install -y mongodb-org

-Start MongoDB server by running this command.

sudo systemctl start mongod

-Check status by running this command

sudo systemctl status mongod


3)yarn 3.4.1+ package manager (tested in v1.22.19 and v4.0.0-rc.35)

npm install --global yarn


4)You must verify that you have these packages installed on your OS :

g++ gcc libgcc libstdc++ linux-headers make python libtool automake autoconf nasm wkhtmltopdf vips vips-dev libjpeg-turbo libjpeg-turbo-dev


Installing Aquila:
Follow the github https://github.com/AquilaCMS/AquilaCMS




