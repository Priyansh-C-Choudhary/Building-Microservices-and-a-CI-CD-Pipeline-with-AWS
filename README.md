 # Building-Microservices-and-a-CI-CD-Pipeline-with-AWS
AWS Academy Lab Project - Microservices and CI/CD Pipeline Builder [92187]

In this project, we are challenged to use at least 11 AWS offerings, including some that might be new to we, to build a microservices and continuous integration and continuous development (CI/CD) solution. 

By the end of this project, we will be able to do the following:

1. Recognize how a Node.js web application is coded and deployed to run and connect to a relational database where the application data is stored.
2. Create an AWS Cloud9 integrated development environment (IDE) and a code repository (repo) in which to store the application code.
3. Split the functionality of a monolithic application into separate containerized microservices.
4. Use a container registry to store and version control containerized microservice Docker images.
5. Create code repositories to store microservice source code and CI/CD deployment assets.
6. Create a serverless cluster to fulfill cost optimization and scalability solution requirements.
7. Configure an Application Load Balancer and multiple target groups to route traffic between microservices.
8. Create a code pipeline to deploy microservices containers to a blue/green cluster deployment.
9. Use the code pipeline and code repository for CI/CD by iterating on the application design.

# Scenario

![image](https://github.com/user-attachments/assets/40f4279e-2198-46c8-b736-d01c425a0e20)

The owners of a café corporation with many franchise locations have noticed how popular their gourmet coffee offerings have become. 

Customers (the café franchise location managers) cannot seem to get enough of the high-quality coffee beans that are needed to create amazing cappuccinos and lattes in their cafés. 

Meanwhile, the employees in the café corporate office have been challenged to consistently source the highest-quality coffee beans. Recently, the leaders at the corporate office learned that one of their favorite coffee suppliers wants to sell her company. The café corporate managers jumped at the opportunity to buy the company. The acquired coffee supplier runs a coffee supplier listings application on an AWS account, as shown in the following image.

The coffee suppliers application currently runs as a monolithic application. It has reliability and performance issues. That is one of the reasons that we have recently been hired to work in the café corporate office. In this project, we perform tasks that are associated with software development engineer (SDE), app developer, and cloud support engineer roles.

We have been tasked to split the monolithic application into microservices, so that we can scale the services independently and allocate more compute resources to the services that experience the highest demand, with the goal of avoiding bottlenecks. A microservices design will also help avoid single points of failure, which could bring down the entire application in a monolithic design. With services isolated from one another, if one microservice becomes temporarily unavailable, the other microservices might remain available.

We have also been challenged to develop a CI/CD pipeline to automatically deploy updates to the production cluster that runs containers, using a blue/green deployment strategy. 

# Approach

![image](https://github.com/user-attachments/assets/e1b14fec-a9e0-4a60-afa8-5e27768883f0)

## Phase 1: Planning the design

We will be using the following services:
1. AWS Cloud9 environment
2. Amazon Virtual Private Cloud (Amazon VPC)
3. Amazon EC2: Instances, Application Load Balancer, target groups
4. AWS CodeCommit: Repository
5. AWS CodeDeploy
6. AWS CodePipeline: Pipeline
7. Amazon Elastic Container Service (Amazon ECS): Services, containers, tasks
8. Amazon Elastic Container Registry (Amazon ECR): Repository
9. AWS Identity and Access Management (IAM): Roles
10. Amazon Relational Database Service (Amazon RDS)
11. Amazon CloudWatch: Logs

Below is a basic architectural diagram usinng draw.io
![Presentation1](https://github.com/user-attachments/assets/8a3faa48-bb4c-4147-aa61-97fb508b9e7a)

## Phase 2: Setup the networking and security groups required

### Networking
The setup will look like this:
Create a VPC with 2 Public and 2 Private Subnets and 1 Internet Gateway:
![image](https://github.com/user-attachments/assets/3d6eb191-74b7-48fe-b39a-0fa865eaa358)

### Security Groups
Create a security group for the EC2 Nodes:
![image](https://github.com/user-attachments/assets/3c73b2ff-c30b-4705-9965-20433c19a58b)
Create a security group for the database:
![image](https://github.com/user-attachments/assets/1739b748-d705-4b1b-9e50-ab504e300af2)
Create a security group for the Application Load Balancer:
![image](https://github.com/user-attachments/assets/19aa4a1a-e263-432b-b940-46a9abbd18ca)

IAM Roles:
![image](https://github.com/user-attachments/assets/435fcbd2-1f27-4eaa-a6cb-9f4194a4309b)
![image](https://github.com/user-attachments/assets/461c2475-1081-496f-b1ad-60eacb94a36e)

## Phase 3: Setting up the MySQL RDS Database and upload the code

### Task 3.1: Provisioning
![image](https://github.com/user-attachments/assets/40bc0388-6e01-496b-893f-1284de7214a7)

Setup The RDS Database with following configurations:
- DB Instance Identifier: supplierdb
- DB Engine Version: 8.0.35
- DB Instance Class: db.t3.micro, 2 vCPUs, 1 GiB RAM, Up to 2,085 Mbps network
- Storage:
Type: General Purpose SSD (gp2), Allocated Storage: 20 GiB
- Availability & Durability:
Multi-AZ Deployment: No
- Connectivity:
Network Type: IPv4, Security Group: DBSecurityGroup

### Task 3.2: Configure database dump on RDS

Now we have a database dump of the required database, we need to configure it on RDS

To access the RDS we will create a EC2 Instance and Install MySQL Client on it.

EC2 Instance Details:
![image](https://github.com/user-attachments/assets/b2665c86-7499-4810-8f41-db02caacc0db)

*Note: For The Security Group Choose EC2Node Security Group

Connect to the EC2 Instance and run the following commands:
```
sudo apt update
sudo apt install nmap
nmap -Pn <db endpoint>  - used to check to verify that the database can be reached from the rdsdatabaseaccess instance on the standard MySQL port number.
sudo apt install mysql-client -y
```
![image](https://github.com/user-attachments/assets/4a9ad81d-5d55-46d8-ac3d-269af90b32bc)

First, connect to the ec2 machine through our Local Machine using SSH
Commands:
```
ssh -i <"Path to Pem File"> ubuntu@<public ip>
logout
```
![image](https://github.com/user-attachments/assets/ee527122-1650-413d-b545-f70318052600)


Second, Upload the Dump File to EC2
```
scp -i <"Path to Pem File"> path\to\wer_dump_file.sql ubuntu@<public ip>:~
```
![image](https://github.com/user-attachments/assets/d5d1a489-2015-4244-b199-b212ca19d345)

Third, Upload the dump file to RDS

Use these commands in the ec2
```
mysql -h <db endpoint> -u wer-username -p
EXIT;
mysql -h <db endpoint> -u wer-username -p < coffee_database_dump.sql
```
*Note: we might need to remove some advanced commands, due to some permission issue. (I recommend using GithHub Copilot/ChatGPT/Phind for solving the error)
![image](https://github.com/user-attachments/assets/e936ba0b-b69d-4ec7-9993-46d0b895eb1b)

Now the databse is ready

### Task 3.3: Upload the monolithic application code

Follow the steps as in Task 3.2 to similarly upload the code on the ec2 instance

![image](https://github.com/user-attachments/assets/d4c55a74-4c7f-4d75-b129-c1f604f1810a)


## Phase 4: Creating a development environment and checking code into a Git repository

### Task 4.1: Create an AWS Cloud9 IDE as wer work environment
![image](https://github.com/user-attachments/assets/9b1ef1a8-6de8-42d2-9d59-b2587cc02126)
![image](https://github.com/user-attachments/assets/0eb65e92-6f92-4f43-8399-7567b29ba7fb)

### Task 4.2: Copy the application code to wer IDE
- Upload the .pem file to wer AWS Cloud9 IDE, and use the Linux ```chmod 400 <file name>``` command to set the proper permissions on the file so that we can use it to connect to an EC2 instance.
- Create a temp directory on the AWS Cloud9 instance at /home/ec2-user/environment/temp.
- From the Amazon EC2 console, retrieve the private IPv4 address of the ec2 instance.
- Use the Linux scp command in the Bash terminal on the AWS Cloud9 instance to copy the source code for the node application from the MonolithicAppServer instance to the temp directory that we created on the AWS Cloud9 instance.
- The following snippet provides an example scp command:
```
scp -r -i ~/environment/key.pem ubuntu@$appServerPrivIp:/home/ubuntu/resources/codebase_partner/* ~/environment/temp/
```
- In the file browser of the IDE, verify that the source files for the application have been copied to the temp directory on the AWS Cloud9 instance.
![image](https://github.com/user-attachments/assets/615fae0a-d179-49d5-a2fc-296d8782c0e5)

### Task 4.3: Create working directories with starter code for the two microservices

Split the monolithic application into two microservices named customer and employee

![image](https://github.com/user-attachments/assets/a5b43038-c71a-44ef-bd71-07ab319fef7d)

![image](https://github.com/user-attachments/assets/d89dc7ab-1a4d-44c7-afcf-9a65d778c941)

### Task 4.4: Create a Git repository for the microservices code and push the code to CodeCommit

![image](https://github.com/user-attachments/assets/7077c19c-3717-4fd9-9a28-c8ae4f20d77a)

![image](https://github.com/user-attachments/assets/9527a2e1-37ba-42e0-b8c6-b1aaa93dcc74)

![image](https://github.com/user-attachments/assets/96c4600a-1474-4e12-aa61-088fc313cbe1)

![image](https://github.com/user-attachments/assets/7284dc26-ba04-4246-a7b9-bf8b21d9bbd7)

## Phase 5: Configuring the application as two microservices and testing them in Docker containers

Task 5.1: Adjust the AWS Cloud9 instance security group settings

![image](https://github.com/user-attachments/assets/019bf92f-ff89-47d6-9513-d3dd63191b05)

Task 5.2: Modify the source code of the customer microservice

![image](https://github.com/user-attachments/assets/0c593bc9-1762-4e83-be60-41621f2ef949)

![image](https://github.com/user-attachments/assets/f123f2c7-0dd0-4824-90f9-0e71bb98a0c9)

![image](https://github.com/user-attachments/assets/60ab39a2-f543-47e2-9c30-013b14c68dca)

![image](https://github.com/user-attachments/assets/2c77f404-c1e3-4420-a898-235f61834ed4)

![image](https://github.com/user-attachments/assets/6b5d2ce1-2ab6-4b61-97d8-31cd6a2eb401)

![image](https://github.com/user-attachments/assets/5b847150-fe81-41ad-85c8-046b9f1e6ce4)

Task 4.3: Create the customer microservice Dockerfile and launch a test container


![image](https://github.com/user-attachments/assets/195faf8e-18f8-44d0-a2db-c4969eef42f7)

![image](https://github.com/user-attachments/assets/f4656aa5-3f6f-4879-ade1-ae91db9b4d48)

![image](https://github.com/user-attachments/assets/69e54c5d-caed-4cf2-a186-31ce716103b8)

![image](https://github.com/user-attachments/assets/968c98c7-4afb-4f0b-a6c8-ae33d2ab746f)

![image](https://github.com/user-attachments/assets/84f7eaa9-a7bf-45ca-aaed-908eea30348a)

![image](https://github.com/user-attachments/assets/e173ffb8-3d74-4fa7-877a-f3a3c48fe701)

![image](https://github.com/user-attachments/assets/e302f016-1a7f-4982-9596-d2f259726218)

![image](https://github.com/user-attachments/assets/89439436-4586-44aa-92c8-162b79b60c3a)

Task 4.4: Modify the source code of the employee microservice

![image](https://github.com/user-attachments/assets/53d2a2e1-cfa9-4940-a1eb-8ead531d9a0d)

![image](https://github.com/user-attachments/assets/065d3aca-b871-45eb-8afc-e83e9686e2f6)

![image](https://github.com/user-attachments/assets/fbc5e090-2f5b-4195-9689-04bc543297d4)

![image](https://github.com/user-attachments/assets/ec51064a-9723-4898-8f60-1af96a9924e6)

![image](https://github.com/user-attachments/assets/128052e8-3aa7-4dde-b1a4-3cbce6c52cf6)

![image](https://github.com/user-attachments/assets/749f412e-26f9-4d36-ae6a-c5faeb6f61a2)

Task 4.5: Create the employee microservice Dockerfile and launch a test container

![image](https://github.com/user-attachments/assets/ffd8c4ec-fb28-4a33-ad04-0d8fa8935f2e)

![image](https://github.com/user-attachments/assets/d5f1dc6b-90a4-47e4-9f91-0a792cd3ad76)

![image](https://github.com/user-attachments/assets/1d446b62-9ceb-4bb2-9c5a-1acca4b46608)

![image](https://github.com/user-attachments/assets/8768b9a5-ab97-45ee-be0f-05642f4c301a)

Task 4.6: Adjust the employee microservice port and rebuild the image

![image](https://github.com/user-attachments/assets/5725164f-5df8-43c5-a6b0-b613dcc499c0)

Task 4.7: Check code into CodeCommit

![image](https://github.com/user-attachments/assets/fd83a44a-0bf1-4cb0-aa5f-44d60e454693)

![image](https://github.com/user-attachments/assets/565f07d2-7694-45b2-90ad-84345a6e8669)

## Phase 6: Creating ECR repositories, an ECS cluster, task definitions, and AppSpec files

Task 5.1: Create ECR repositories and upload the Docker images

![image](https://github.com/user-attachments/assets/1ca76114-bb0c-4fe6-ae43-3d3b9c815582)

![image](https://github.com/user-attachments/assets/20407d5b-8765-4a27-af81-a65ba03351a4)

![image](https://github.com/user-attachments/assets/427e3171-03b2-41f2-b267-ac57cdaca730)

![image](https://github.com/user-attachments/assets/7c111b98-1fb6-4afb-8450-c2bc0912dc64)

![image](https://github.com/user-attachments/assets/11b44b5a-91e4-4b8d-8f87-5bb1f94bab7e)

![image](https://github.com/user-attachments/assets/a36ef388-32b4-470c-a4b4-aa2211cdaa14)

![image](https://github.com/user-attachments/assets/98a11345-02cf-4ad5-bfe6-541867014694)

Task 5.2: Create an ECS cluster

![image](https://github.com/user-attachments/assets/26e93245-d1bf-40e3-b756-8bcae8cdd516)

![image](https://github.com/user-attachments/assets/811794fc-70d5-48b5-81f8-a7981acc0c3d)

![image](https://github.com/user-attachments/assets/a880a194-ca4b-47b9-be4e-765bff63b123)

Task 5.3: Create a CodeCommit repository to store deployment files

![image](https://github.com/user-attachments/assets/b9043d2f-ae3e-48f3-8aba-3c2583e7a771)

![image](https://github.com/user-attachments/assets/ccf48218-0c66-4191-9950-cb9d7876e8c7)

Task 5.4: Create task definition files for each microservice and register them with Amazon ECS

![image](https://github.com/user-attachments/assets/08e80d74-f7ca-482b-9ad9-6cfbee05c3b6)

![image](https://github.com/user-attachments/assets/1f656c48-805b-46c7-81e6-e8985814caf3)

![image](https://github.com/user-attachments/assets/33d4d402-9b7e-4d03-9035-177867334ad7)

Task 5.5: Create AppSpec files for CodeDeploy for each microservice

![image](https://github.com/user-attachments/assets/6f1cb05e-4c72-4118-8d46-a7c9f8a5a0ad)

Task 5.6: Update files and check them into CodeCommit

![image](https://github.com/user-attachments/assets/8935eb81-b10a-4c4e-a001-c658e5d81891)

![image](https://github.com/user-attachments/assets/48b8b493-e847-4c6d-a9f9-f36d5317a24d)

## Phase 7: Creating target groups and an Application Load Balancer

Task 6.1: Create four target groups

In this task, we will create four target groups—two for each microservice. Because we will configure a blue/green deployment, CodeDeploy requires two target groups for each deployment group.

![image](https://github.com/user-attachments/assets/b26aef6a-741c-42b9-a05a-ac748b05c8c7)

![image](https://github.com/user-attachments/assets/08838006-28a7-4360-bc28-6d115e15fa83)

Task 6.2: Create a security group and an Application Load Balancer, and configure rules to route traffic

In this task, we will create an Application Load Balancer. we will also define two listeners for the load balancer: one on port 80 and another on port 8080. For each listener, we will then define path-based routing rules so that traffic is routed to the correct target group depending on the URL that a user attempts to load.

![image](https://github.com/user-attachments/assets/bf2e2e68-d044-4ee4-841d-92d0dcc6e877)

![image](https://github.com/user-attachments/assets/0c423981-2b94-4181-a6ee-f3bcfdf459a7)

![image](https://github.com/user-attachments/assets/ade5b932-4059-4596-8533-855e286bd89a)

![image](https://github.com/user-attachments/assets/742f08fc-6f24-41c6-9f6c-621a11255108)

![image](https://github.com/user-attachments/assets/f19c82f4-bbb8-4647-b2c9-8c64c499b51e)

## Phase 8: Creating two Amazon ECS services
In this phase, we will create a service in Amazon ECS for each microservice.

Task 7.1: Create the ECS service for the customer & employee microservice

![image](https://github.com/user-attachments/assets/2e946551-da11-4a23-8670-7bd09cf05017)

![image](https://github.com/user-attachments/assets/52def829-780f-42c4-a952-74adefdd2145)

## Phase 9: Configuring CodeDeploy and CodePipeline

Task 8.1: Create a CodeDeploy application and deployment groups

A CodeDeploy application is a collection of deployment groups and revisions. A deployment group specifies an Amazon ECS service, load balancer, optional test listener, and two target groups. A group specifies when to reroute traffic to the replacement task set, and when to terminate the original task set and Amazon ECS application after a successful deployment.

![image](https://github.com/user-attachments/assets/435eaf80-3cd1-4733-b07b-d6eba0a37f87)

![image](https://github.com/user-attachments/assets/2c2c04c6-c97c-443f-8b3d-074e9bc017cd)

Task 8.2: Create a pipeline for the customer microservice

In this task, we will create a pipeline to update the customer microservice. When we first define the pipeline, we will configure CodeCommit as the source and CodeDeploy as the service that is responsible for deployment. we will then edit the pipeline to add the Amazon ECR service as a second source.

With an Amazon ECS blue/green deployment, which we will specify in this task, we provision a new set of containers, which CodeDeploy installs the latest version of wer application on. CodeDeploy then reroutes load balancer traffic from an existing set of containers, which run the previous version of wer application, to the new set of containers, which run the latest version. After traffic is rerouted to the new containers, the existing containers can be terminated. With a blue/green deployment, we can test the new application version before sending production traffic to it.

![image](https://github.com/user-attachments/assets/2ba33d65-9307-4354-bd8a-1840680e5ea2)
![image](https://github.com/user-attachments/assets/0fa1aed1-6332-4cce-ad2e-49394f2dd283)
![image](https://github.com/user-attachments/assets/71484c5f-ad06-496b-a92f-e8d1c14b1b64)

Task 8.3: Test the CI/CD pipeline for the customer microservice
![image](https://github.com/user-attachments/assets/c6c9d670-1c3f-43a6-b7a9-e697d206f662)
![image](https://github.com/user-attachments/assets/92e471ec-f4a4-4a25-a0b5-f355f104d9e1)
![image](https://github.com/user-attachments/assets/f478a49c-179f-4032-bfd8-eb31fbdcf551)
![image](https://github.com/user-attachments/assets/252a16cc-4e33-46a6-b854-50e4a73a684e)
![image](https://github.com/user-attachments/assets/cac9fa97-dd98-4c60-bd20-b703f7009135)
![image](https://github.com/user-attachments/assets/a4a3216a-9187-4e20-9df6-5f5d40759ffc)
![image](https://github.com/user-attachments/assets/aa0e8c2f-7799-4ce9-b18d-6f702b1540f8)

Task 8.4: Create a pipeline for the employee microservice
![image](https://github.com/user-attachments/assets/6ee09199-ee03-4a70-87a3-740b0a5fff7a)

Task 8.5: Test the CI/CD pipeline for the employee microservice
![image](https://github.com/user-attachments/assets/e0eb7ae4-465f-4e51-ab01-92795257f7df)
![image](https://github.com/user-attachments/assets/6c71c62d-0914-4e88-8a13-052d6c825451)

## Phase 10: Adjusting the microservice code to cause a pipeline to run again





