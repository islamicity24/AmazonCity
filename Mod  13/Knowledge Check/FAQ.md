## What are characteristics of component in a microservice architecture ?
In a microservice architecture, components are individual, autonomous services that work together to provide the overall functionality of an application. Some common characteristics of components in a microservice architecture include:

**Independent deployment**: Each component can be deployed independently of other components, allowing for greater flexibility and agility in the development and deployment process.

**Autonomous functionality**: Each component is responsible for a specific function or set of functions, and can operate independently of other components.

**Bounded context**: Each component has a well-defined domain or scope of responsibility, which is known as its bounded context. This helps to ensure that each component is focused on a specific business capability or feature.

**API-driven communication**: Components communicate with each other through APIs, which allows them to be loosely coupled and independently developed.

**Resilience**: Components are designed to be fault-tolerant and able to handle failure gracefully. This means that if one component fails, it does not bring down the entire system.

**Scalability**: Components can be scaled independently of each other, allowing for greater flexibility in handling changes in demand or usage.

**Technology diversity**: Each component can use a different technology stack, programming language, or framework, allowing for greater flexibility and choice in development.

## What are characteristics of component in a microservice architecture ? (select THREE)  
1. A. Open-source code 
2. B. Industry-standard interfaces 
3. C. Hidden implementation 
4. D. Specialized function 
5. E. HTTP(S) communication 
6. F. Independence from other component


The characteristics of components in a microservice architecture are:

B. Industry-standard interfaces
D. Specialized function
F. Independence from other component

Components in a microservice architecture should have industry-standard interfaces to communicate with each other, be designed to perform a **specialized function**, and be **independent of other components**, allowing them to **be deployed and scaled separately**. The other options listed are not characteristics of components in a microservice architecture.


## What is AWS Fargate?
AWS Fargate is a compute engine for Amazon Elastic Container Service (ECS) and Amazon Elastic Kubernetes Service (EKS) that allows you to run containers without having to manage the underlying infrastructure. It is a serverless compute engine for containers, meaning that you don't have to provision, configure, or manage servers to run your applications.

With AWS Fargate, you simply define the containerized applications that you want to run and specify the resources they require, such as CPU and memory. AWS Fargate then launches and scales the necessary infrastructure to run your containers, and automatically manages the underlying infrastructure, including patching, scaling, and availability.

AWS Fargate provides a secure and flexible platform for running containerized applications, with support for both Linux and Windows containers. It also integrates with other AWS services, such as Amazon CloudWatch for monitoring and AWS Identity and Access Management (IAM) for authentication and authorization.

What is AWS Fargate?
1. A fully managed service for orchestrating container clusters
2. A tool for developing software that uses field-programmable gate arrays (FPGA) 
3. A service that seamlessly extends AWS services to edge devices  
4. A service that enables you to run containers without needing to manage servers of clusters

Option 4 is correct: AWS Fargate is a service that enables you to run containers without needing to manage servers or clusters.

## What is a container?
A container is a lightweight, standalone, and executable software package that contains everything needed to run an application, including the code, libraries, dependencies, and configurations. Containers provide a consistent runtime environment that can run reliably and consistently across different computing environments, including development, testing, staging, and production.

Containers are built from images, which are a snapshot of an application and its dependencies. Containers can be started or stopped quickly, making them a popular choice for deploying applications in modern, dynamic computing environments, such as cloud computing and microservices architectures. They can be run on physical servers, virtual machines, or cloud platforms, and can be managed using container orchestration tools, such as Kubernetes or Docker Swarm.

Some benefits of using containers include increased portability, scalability, and efficiency, as well as simplified application deployment and management. Containers can also help to improve security by isolating applications and their dependencies from the underlying host system.


## What is a serverless architecture in Amazon Web Services (AWS) ?
Serverless architecture in Amazon Web Services (AWS) refers to a computing model where the cloud provider manages the infrastructure and automatically allocates and scales resources needed to run applications. In a serverless architecture, developers focus solely on writing code without worrying about managing and provisioning servers, storage, or other infrastructure components.

AWS offers various serverless services such as AWS Lambda, Amazon API Gateway, AWS Step Functions, AWS AppSync, and AWS DynamoDB. AWS Lambda is a serverless compute service that lets you run code without provisioning or managing servers. You upload your code to AWS Lambda, and it automatically scales to handle requests. Amazon API Gateway allows you to create, deploy, and manage APIs for your applications, while AWS Step Functions provides a way to build serverless workflows for your applications.

One of the main benefits of serverless architecture is that you only pay for the actual resources used by your application, and you can scale up or down automatically based on demand. This can help reduce costs and improve efficiency. Serverless architecture can also help simplify application development and deployment, as well as improve availability and scalability.


## What are benefits of serverless computing in Amazon Web Services (AWS)
There are several benefits of serverless computing in Amazon Web Services (AWS):

**Cost savings**: With serverless computing, you only pay for the exact amount of resources used by your application. This can help reduce costs by eliminating the need to provision and manage servers that are not fully utilized.

**Scalability**: Serverless computing automatically scales resources up or down based on demand. This allows you to handle sudden increases in traffic without worrying about capacity planning or managing infrastructure.

**Availability**: Serverless computing can help improve availability and resilience of your application by automatically replicating resources across multiple availability zones.

**Reduced operational overhead**: Since AWS manages the underlying infrastructure for serverless computing, there is less operational overhead for you to worry about. This allows you to focus on developing and deploying your applications.

**Faster time-to-market**: With serverless computing, you can develop and deploy applications more quickly by focusing on the application code rather than infrastructure management.

**Greater flexibility**: Serverless computing allows you to use a variety of programming languages and runtimes, and easily integrate with other AWS services. This provides greater flexibility for building and deploying applications.

**Improved security**: Serverless computing can help improve security by limiting the attack surface and allowing you to apply security controls at different levels of your application stack.


## Which workflows are supported by aws Step Function?
AWS Step Functions supports a variety of workflows, including:

**Standard workflows**: These workflows execute a sequence of AWS Lambda functions, and can also include other AWS services and APIs.

**Express workflows**: These workflows execute a single AWS Lambda function and are designed for use cases that require faster execution times and lower latencies.

**Fan-out/fan-in workflows**: These workflows are used for parallel processing of multiple AWS Lambda functions, which can help improve throughput and reduce processing times.

**Map workflows**: These workflows enable you to process a large set of data in parallel by dividing it into smaller chunks and processing them independently.

**Choice workflows**: These workflows allow you to build conditional logic into your Step Functions, enabling you to make decisions based on the input or output of previous steps.

**Wait workflows**: These workflows enable you to introduce wait times between steps, allowing you to orchestrate long-running processes.

**Error handling workflows**: These workflows enable you to handle errors and retries in your Step Functions, helping to improve the resiliency and reliability of your applications.

**State machine workflows**: These workflows are used to manage the state of your application, providing a visual representation of your application's progress and enabling you to easily identify and diagnose issues.

