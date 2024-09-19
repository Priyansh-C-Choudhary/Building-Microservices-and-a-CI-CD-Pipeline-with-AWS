# Building-Microservices-and-a-CI-CD-Pipeline-with-AWS
AWS Academy Lab Project - Microservices and CI/CD Pipeline Builder [92187]

In this project, we are challenged to use at least 11 AWS offerings, including some that might be new to you, to build a microservices and continuous integration and continuous development (CI/CD) solution. 

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

The coffee suppliers application currently runs as a monolithic application. It has reliability and performance issues. That is one of the reasons that you have recently been hired to work in the café corporate office. In this project, we perform tasks that are associated with software development engineer (SDE), app developer, and cloud support engineer roles.

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

## Phase 3: Setting up the MySQL RDS Database

### Provisioning
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

### Configure database dump on RDS

Now we have a database dump of the required database, we need to configure it on RDS

To access the RDS we will create a EC2 Instance and Install MySQL Client on it.

EC2 Instance Details:
![image](https://github.com/user-attachments/assets/b2665c86-7499-4810-8f41-db02caacc0db)

*Note: For The Security Group Choose EC2Node Security Group

Connect to the EC2 Instance and run the following commands:
```
sudo apt update
sudo apt install nmap <db endpoint> - used to check to verify that the database can be reached from the rdsdatabaseaccess instance on the standard MySQL port number.
sudo apt install mysql-client -y
```
