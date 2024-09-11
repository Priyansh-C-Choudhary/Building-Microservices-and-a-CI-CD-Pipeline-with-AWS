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

## Phase 1: Planning the design and estimating cost

### Task 1.1: Create an architecture diagram

We will be using the following services:
Amazon Virtual Private Cloud (Amazon VPC)
Amazon EC2: Instances, Application Load Balancer, target groups
AWS CodeCommit: Repository
AWS CodeDeploy
AWS CodePipeline: Pipeline
Amazon Elastic Container Service (Amazon ECS): Services, containers, tasks
Amazon Elastic Container Registry (Amazon ECR): Repository
AWS Cloud9 environment
AWS Identity and Access Management (IAM): Roles
Amazon Relational Database Service (Amazon RDS)
Amazon CloudWatch: Logs


