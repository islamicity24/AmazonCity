# Module 6 - Challenge Lab: Creating a VPC Networking Environment for the Café
**Scenario**
Sofía and Nikhil are now confident in their ability to create a two-tier architecture because of their experience migrating the café's data. They successfully moved from a MariaDB database on an Amazon Elastic Compute Cloud (Amazon EC2) instance to an Amazon Relational Database Service (Amazon RDS) database instance. In addition, they also moved their database resources from a public subnet to a private subnet.

Mateo

When Mateo—a café regular and an AWS systems administrator and engineer—visits the café, Sofía and Nikhil tell him about the database migration. Mateo tells them that they can enhance security by running the café's application server in another private subnet that's separate from the database instance. They could then go through a bastion host (or jump box) to gain administrative access to the application server. The application server must also be able to download needed patches.

Knowing that the cloud makes experimentation easier, Sofía and Nikhil are eager to set up a non-production VPC environment. They can use it implement the new architecture and test different security layers, without accidentally disrupting the café's production environment.

# Lab Overview and Objectives

In this lab, you will use Amazon Virtual Private Cloud (Amazon VPC) to create a networking environment on AWS and implement security layers to protect your resources.

After completing this lab, you should be able to:

- Create a virtual private cloud (VPC) environment that enables you to securely connect to private resources
- Enable your private resources to connect to the internet
- Create an additional layer of security in your VPC to control traffic to and from private resources

When you start the lab, you will only have a VPC created for you in the AWS account.

At the end of this lab, your architecture should look like the following example:

![AmazonCity Architecture](https://github.com/islamicity24/AmazonCity/raw/main/module-6-challenge-lab-final-architecture.png)
![final architecture](./final-architecture.png)

_Note: in this challenge lab, step-by-step instructions are not provided for most of the tasks. You must figure out how to complete the tasks on your own._

## Challenge #1: A business request for the café: Creating a VPC network that allows café staff to remotely and securely administer the web application server 

In this challenge, you will take on the role of one of the café's system administrators. You will create and configure `a VPC network` so that you can securely connect from `a bastion host` in `a public subnet` to an EC2 instance in `a private subnet`. You will also create `a NAT gateway` to enable the EC2 instance in your private subnet to access the internet.

### Task 1: Creating a public subnet

Your first task in this lab is to create a `public subnet` in the `Lab VPC`. After you create a public subnet, you will create an `internet gateway` to allow communication from the subnet to the internet. You will update the `routing table` that's attached to the subnet to `route internet-bound network traffic  through the internet gateway.

5. Open the Amazon VPC console.
6. Note that a VPC called `Lab VPC` was created for you.
7. Create a public subnet that meets the following criteria:
   - Name tag: Public Subnet
   - VPC: Lab VPC
   - Availability Zone: Choose Availability Zone a of your Region (for example, if your Region is us-east-1, then select us-east-1a)
   - IPv4 CIDR block: 10.0.0.0/24
8. Create a new internet gateway and attach it to the Lab VPC.
9. Edit the route table that was created in your VPC. Add the route 0.0.0.0/0. For the target, select the internet gateway that you created in the previous step.

> Hint: To successfully complete this task, you must create a few resources. If you get stuck, refer to the AWS Documentation._

Here's the AWS CLI code to complete Task 1 of Challenge #1:<br>
step 1
```
aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.0.0/24 --availability-zone <availability-zone> --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=Public Subnet}]'
```
Replace <vpc-id> with the ID of the Lab VPC created for this challenge, and <availability-zone> with the availability zone of your choice within your region.<br>
step 2   
```
aws ec2 create-internet-gateway --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=Lab VPC IGW}]'
```
```
aws ec2 attach-internet-gateway --internet-gateway-id <internet-gateway-id> --vpc-id <vpc-id>
```
   Replace <internet-gateway-id> with the ID of the internet gateway created in step 2, and <vpc-id> with the ID of the Lab VPC.
```
aws ec2 create-route --route-table-id <route-table-id> --destination-cidr-block 0.0.0.0/0 --gateway-id <internet-gateway-id>
```
   Replace <route-table-id> with the ID of the route table associated with the Public Subnet created in step 1, and <internet-gateway-id> with the ID of the internet gateway created in step 2.
   
### Task 2: Creating a bastion host

In this task, you will `create a bastion host` in the `Public Subnet`. In later tasks, you will create `an EC2 instance` in `a private subnet` and connect to it from this bastion host.

10. From the Amazon EC2 console, create an EC2 instance in the Public Subnet of the Lab VPC that meets the following criteria:
   - Amazon Machine Image (AMI): Amazon Linux 2 AMI (HVM)
   - Instance type: t2.micro
   - Auto-assign Public IP: `This setting should be **disabled**`
   - Name: Bastion Host
   - Security group called `Bastion Host SG` that only allows the following traffic:
     - Type: SSH
     - Port: 22
     - Source: Your IP address
   - Uses the **vockey** key pair
    
> Note: In practice, hardening a bastion host involves more work than only restricting `Secure Shell (SSH)` traffic from your IP address. A bastion host is typically placed in a network that's closed off from other networks. It's often protected with multi-factor authentication (MFA) and monitored with auditing tools. Most enterprises require an auditable access trail to the bastion host.

**step 1**
```
aws ec2 create-security-group --group-name "Bastion Host SG" --description "Security group for Bastion Host" --vpc-id <vpc-id>
```
Replace `<vpc-id>` with the ID of the Lab VPC created for this challenge.
```
aws ec2 authorize-security-group-ingress --group-name "Bastion Host SG" --protocol tcp --port 22 --cidr <your-ip-address>/32
```
Replace `<your-ip-address>` with your public IP address.
```
aws ec2 create-key-pair --key-name bastion-key --query 'KeyMaterial' --output text > bastion-key.pem && chmod 400 bastion-key.pem
```
This creates a new key pair called "bastion-key" and saves the private key to a file named "bastion-key.pem". Make sure to securely store this private key, as you will need it later to connect to the bastion host.
```
aws ec2 run-instances --image-id ami-0fc61db8544a617ed --instance-type t2.micro --key-name bastion-key --subnet-id <public-subnet-id> --security-group-ids <security-group-id> --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Bastion Host}]' --no-associate-public-ip-address
```
Replace `<public-subnet-id>` with the ID of the Public Subnet created in Task 1, and `<security-group-id>` with the ID of the security group "Bastion Host SG" created in step 1.
   
> Note: Make sure to run the above commands in the same order as listed above, and replace the required parameters with the corresponding values.
