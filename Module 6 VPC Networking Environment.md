# Lab Overview and Objectives

In this lab, you will use Amazon Virtual Private Cloud (Amazon VPC) to create a networking environment on AWS and implement security layers to protect your resources.

After completing this lab, you should be able to:

- Create a virtual private cloud (VPC) environment that enables you to securely connect to private resources
- Enable your private resources to connect to the internet
- Create an additional layer of security in your VPC to control traffic to and from private resources

When you start the lab, you will only have a VPC created for you in the AWS account.

At the end of this lab, your architecture should look like the following example:

![final architecture](./final-architecture.png)

_Note: in this challenge lab, step-by-step instructions are not provided for most of the tasks. You must figure out how to complete the tasks on your own._

## Challenge #1: A business request for the café: Creating a VPC network that allows café staff to remotely and securely administer the web application server 

In this challenge, you will take on the role of one of the café's system administrators. You will create and configure a VPC network so that you can securely connect from a bastion host in a public subnet to an EC2 instance in a private subnet. You will also create a NAT gateway to enable the EC2 instance in your private subnet to access the internet.

### Task 1: Creating a public subnet

Your first task in this lab is to create a public subnet in the Lab VPC. After you create a public subnet, you will create an internet gateway to allow communication from the subnet to the internet. You will update the routing table that's attached to the subnet to route internet-bound network traffic through the internet gateway.

1. Open the Amazon VPC console.
2. Note that a VPC called Lab VPC was created for you.
3. Create a public subnet that meets the following criteria:
   - Name tag: Public Subnet
   - VPC: Lab VPC
   - Availability Zone: Choose Availability Zone a of your Region (for example, if your Region is us-east-1, then select us-east-1a)
   - IPv4 CIDR block: 10.0.0.0/24
4. Create a new internet gateway and attach it to the Lab VPC.
5. Edit the route table that was created in your VPC. Add the route 0.0.0.0/0. For the target, select the internet gateway that you created in the previous step.

_Hint: To successfully complete this task, you must create a few resources. If you get stuck, refer to the AWS Documentation._

### Task 2: Creating a bastion host

In this task, you will create a bastion host in the Public Subnet. In later tasks, you will create an EC2 instance in a private subnet and connect to it from this bastion host.

1. From the Amazon EC2 console, create an EC2 instance in the Public Subnet of the Lab VPC that meets the following criteria:
   - Amazon Machine Image (AMI): Amazon Linux 2 AMI (HVM)
   - Instance type: t2.micro
   - Auto-assign Public IP: This setting should be disabled
   - Name: Bastion Host
   - Security group called Bastion Host SG that only allows the following traffic:
     - Type: SSH
     - Port: 22
     - Source: Your IP address
     - Uses the vockey key pair
2. Note: In practice, hardening a bastion host involves more
