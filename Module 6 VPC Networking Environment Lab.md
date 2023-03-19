# Module 6 - Challenge Lab: Creating a VPC Networking Environment for the Café
**Scenario**
Sofía and Nikhil are now confident in their ability to create a two-tier architecture because of their experience migrating the café's data. They successfully moved from a MariaDB database on an Amazon Elastic Compute Cloud (Amazon EC2) instance to an Amazon Relational Database Service (Amazon RDS) database instance. In addition, they also moved their database resources from a public subnet to a private subnet.

Mateo

When Mateo—a café regular and an AWS systems administrator and engineer—visits the café, Sofía and Nikhil tell him about the database migration. Mateo tells them that **they can enhance security by running the café's application server in another private subnet that's separate from the database instance**. `They could then go through a bastion host (or jump box) to gain administrative access to the application server`. The application server must also be able to download needed patches.

Knowing that `the cloud makes experimentation easier`, Sofía and Nikhil are eager to set up a non-production VPC environment. They can use it implement the new architecture and test different security layers, without accidentally disrupting the café's production environment.

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

> _Note: in this challenge lab, step-by-step instructions are not provided for most of the tasks. You must figure out how to complete the tasks on your own._

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
**step 1**
```
aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.0.0/24 --availability-zone <availability-zone> --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=Public Subnet}]'
```
Replace `<vpc-id>` with the ID of the Lab VPC created for this challenge, and <availability-zone> with the availability zone of your choice within your region.<br>
**step 2**   
```
aws ec2 create-internet-gateway --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=Lab VPC IGW}]'
```
```
aws ec2 attach-internet-gateway --internet-gateway-id <internet-gateway-id>   --vpc-id <vpc-id>  --region ap-southeast-3
```
```
aws ec2 attach-internet-gateway --internet-gateway-id "igw-0c880167a2e2dba47" --vpc-id "vpc-078e4a79bcb2ecb6c" --region us-east-1
```   
   Replace `<internet-gateway-id>` with the ID of the internet gateway created in step 2, and `<vpc-id>` with the ID of the Lab VPC.
```
aws ec2 create-route --route-table-id <route-table-id> --destination-cidr-block 0.0.0.0/0 --gateway-id <internet-gateway-id>
```
   Replace `<route-table-id>` with the ID of the route table associated with the Public Subnet created in step 1, and `<internet-gateway-id>` with the ID of the internet gateway created in step 2.
   
### Task 2: Creating a bastion host

In this task, you will `create a bastion host` in the `Public Subnet`. In later tasks, you will create `an EC2 instance` in `a private subnet` and connect to it from this bastion host.

10. From the Amazon EC2 console, create an EC2 instance in the Public Subnet of the Lab VPC that meets the following criteria:
   - **Amazon Machine Image (AMI)**: Amazon Linux 2 AMI (HVM)
   - **Instance type**: t2.micro
   - **Auto-assign Public IP**: `This setting should be **disabled**`
   - **Name**: Bastion Host
   - Security group called `Bastion Host SG` that only allows the following traffic:
     - **Type**: SSH
     - **Port**: 22
     - **Source**: Your IP address ?
   - Uses the **vockey** key pair   (bastion-key ?)
    
> Note: In practice, hardening a bastion host involves more work than only restricting `Secure Shell (SSH)` traffic from your IP address. A bastion host is typically placed in a network that's closed off from other networks. It's often protected with multi-factor authentication (MFA) and monitored with auditing tools. Most enterprises require an auditable access trail to the bastion host.
> Catatan: Pada praktiknya, pengamanan bastion host melibatkan lebih banyak pekerjaan daripada hanya membatasi lalu lintas Secure Shell (SSH) dari alamat IP Anda. Bastion host biasanya ditempatkan di jaringan yang terisolasi dari jaringan lain. Biasanya, bastion host dilindungi dengan otentikasi multi-faktor (MFA) dan dipantau dengan alat audit. Kebanyakan perusahaan memerlukan jejak akses yang dapat diaudit ke bastion host. 

**step 1** Create a security group for the bastion host
```
aws ec2 create-security-group --group-name "Bastion Host SG" --description "Security Group for Bastion Host" --vpc-id <vpc-id>
```
Replace `<vpc-id>` with the ID of the Lab VPC created for this challenge. This command will return a JSON object with the details of the security group, including its GroupId `<security-group-id>`. Make note of this value, as it will be used in the next steps.
   
**step 2** Add a rule to the security group to allow SSH traffic from your IP address:   
```
aws ec2 authorize-security-group-ingress --group-name "Bastion Host SG" --protocol tcp --port 22 --cidr <your-ip-address>/32
```
Replace `<your-ip-address>` with your public IP address.
   
```
aws ec2 create-key-pair --key-name bastion-key --query 'KeyMaterial' --output text > bastion-key.pem && chmod 400 bastion-key.pem
```
This creates a new key pair called "bastion-key" and saves the private key to a file named "bastion-key.pem". Make sure to securely store this private key, as you will need it later to connect to the bastion host.

**step 3** Launch the EC2 instance:
```
aws ec2 run-instances --image-id ami-0fc61db8544a617ed --instance-type t2.micro --key-name bastion-key --subnet-id <public-subnet-id> --security-group-ids <security-group-id> --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Bastion Host}]' --no-associate-public-ip-address
```
Replace `<public-subnet-id>` with the ID of the Public Subnet created in Task 1, and `<security-group-id>` with the ID of the Security Group "Bastion Host SG" created in step 1.
   
step 4 Wait for the instance to be running:
```   
aws ec2 wait instance-running --instance-ids <InstanceId>
```
Replace <InstanceId> with the InstanceId of the launched instance.

Once the instance is running, you can use SSH to connect to it using the key pair specified in step 3.
   
> Note: Make sure to run the above commands in the same order as listed above, and replace the required parameters with the corresponding values.

## Task 3: Allocating an Elastic IP address for the bastion host
In this task, you will assign `an Elastic IP address` to the bastion host.  

The Bastion host that you just created can't be reached from the internet. It doesn't have a public IPv4 address or an Elastic IP address that's associated with its private IPv4 address. Elastic IP addresses are associated with Bastion host instances and are allowed from on-premises firewalls. If an instance is terminated and a new instance is launched in its place, the existing Elastic IP address is re-associated with the new instance. With this behavior, the same trusted Elastic IP address is used at all times.

11. Allocate an Elastic IP address, and make it reachable from the internet over IPv4 by associating it with your bastion host.

To allocate and associate an Elastic IP address to the bastion host using AWS CLI, you can follow these steps:
**step 1** Allocate an Elastic IP address:
```
   aws ec2 allocate-address --domain vpc --region <your-region>
```
**step 2 **  Associate the Elastic IP address with the bastion host:
```
   aws ec2 associate-address --instance-id <your-bastion-host-instance-id> --public-ip <your-elastic-ip-address> --region <your-region>
```
   > Make sure to replace `<your-region>` with the region where your VPC and instance are located, `<your-bastion-host-instance-id>` with the actual instance ID of the bastion host instance you created in Task 2, and `<your-elastic-ip-address>` with the Elastic IP address that you allocated in step 1.

## Task 4: Testing the connection to the bastion host
   
In this task, you will use the SSH key (.pem file or .ppk file) to test the SSH connection to your bastion host. This key was created for you.

12. In the top-right area of these instructions, `select Details` in AWS Challenge Lab Next to AWS, choose `Show`.

14. Download the **SSH key**. Note the file will be named `labuser.*`.

   - Microsoft Windows PuTTY users: `Download PPK`
   - macOS or Linux users: Download PEM

15. To close the window, choose **X**.

16. Connect to your bastion host by using SSH. (from PUTTY)
```
   ssh -i /path/to/labuser.pem ec2-user@<bastion-host-public-ip>
```

After you have tested your connection to the bastion host, you can close the terminal or PuTTY.

**Hint** : If you get stuck, refer to the AWS Documentation [AWS EC2 documentation on using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html)
. This page provides detailed instructions about how to use SSH to connect to an EC2 instance.


> Note for Microsoft Windows users: If you don't have PuTTY installed, you must download and install PuTTY. We recommend that you configure PuTTY so that your connection doesn't expire. To keep the PuTTY session open longer, set Seconds between **keepalives to 30 or 60.**


## Task 5: Creating a private subnet
In this task, you will create `a private subnet` in the `Lab VPC`.

18. In the console, create a private subnet that meets the following criteria:

   - Name tag: Private Subnet
   - Availability Zone: Same as Public Subnet
   - IPv4 CIDR block: 10.0.1.0/24
```
   aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.1.0/24 --availability-zone <availability-zone> --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=Private Subnet}]'
```
   Make sure to replace `<vpc-id>` with the ID of your Lab VPC, and `<availability-zone>` with the same availability zone as the public subnet.

This command creates a new subnet with a CIDR block of 10.0.1.0/24 and adds a name tag of "Private Subnet".

## Task 6: Creating a NAT gateway
In this task, you will create `a NAT gateway`, which enables resources in the 'Private Subnet' to connect to the internet.

19 Create `a NAT gateway` that meets the following criteria:

   - Name: Lab NAT Gateway
   - Subnet: Public Subnet
   
**Tip**: Your NAT gateway needs an Elastic IP address. -->  `Tekan Tombol Allocate Elastic IP`

20. Create a new route table that meets the following criteria:

   - **Name tag**: Private Route Table
   - **Destination**: 0.0.0.0/0
   - **Target**: NAT Gateway
21. Attach this route table to the Private Subnet, which you created earlier.   (Associate) 

**Hint**: If you get stuck, refer to the AWS Documentation [AWS VPC documentation on creating a NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html#nat-gateway-creating)
.
```
aws ec2 create-nat-gateway --subnet-id <Public Subnet ID> --allocation-id <Elastic IP Allocation ID> --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=Lab NAT Gateway}]'
```
   Make sure to replace <Public Subnet ID> with the actual ID of the public subnet, and <Elastic IP Allocation ID> with the actual allocation ID of an Elastic IP address.
   
## Task 7: Creating an EC2 instance in the private subnet
In this task, you will create an EC2 instance in the Private Subnet, and you will configure it to allow SSH traffic from the bastion host. You will also create a new key pair to access this instance.

22. Create a new key pair named `vockey2`, and download the appropriate .ppk (Microsoft Windows) or .pem (macOS or Linux).

23. Create an EC2 instance in the Private Subnet of the Lab VPC that meets the following criteria.
   - AMI: Amazon Linux 2 AMI (HVM)
   - Instance type: t2.micro
   - Name: Private Instance
   - Only allows the following traffic:   
      - Type: SSH
      - Port: 22
      - Source: Bastion host security group (Hint: Refer to the AWS Documentation
   - Uses the `vockey2` key pair that you created earlier

```
aws ec2 create-key-pair --key-name vockey2 --query 'KeyMaterial' --output text > vockey2.pem
```
```
aws ec2 create-key-pair --key-name vockey2 --query 'KeyMaterial' --output text > vockey2.pem
chmod 400 vockey2.pem
```   
   The first two commands create and download the key pair. Make sure to store the private key file (vockey2.pem) in a safe and secure location.
```
aws ec2 run-instances --image-id ami-0fc61db8544a617ed --instance-type t2.micro --subnet-id <private-subnet-id> --security-group-ids <private-instance-sg-id> --key-name vockey2 --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Private Instance}]' --associate-public-ip-address --user-data file://userdata.txt
``` 
   > Note: Replace `<private-subnet-id>` with the ID of the private subnet that you created in Task 5, and `<private-instance-sg-id>` with the ID of the security group that you will create in Task 8 ???.
   > Note that the userdata.txt file should contain the following script to allow SSH traffic from the bastion host:
```
#!/bin/bash
echo "Allowing SSH traffic from Bastion Security Group"
aws ec2 authorize-security-group-ingress --group-id <Security Group ID> --protocol tcp --port 22 --cidr <CIDR Block>
```
Make sure to replace <Security Group ID> with the ID of the security group that allows SSH traffic from the bastion host, and <CIDR Block> with the CIDR block of the VPC (e.g., 10.0.0.0/16).
   
## Task 8: Configuring your SSH client for SSH passthrough
Because the private instance you just created uses a different key pair than the bastion host, you must configure your SSH client to use SSH passthrough. This action allows you to use a key pair that's stored on your computer to access the private instance without uploading the key pair to the bastion host. **This is a good security practice**.  

To set up your client, follow either the Microsoft Windows, or the macOS or Linux steps.

**Microsoft Windows users only**
Windows users should complete the following steps.

24. Download and install Pageant, which is available from the PuTTY download page.

After you install Pageant, open it. Pageant runs as a Windows service.

To import the PuTTY-formatted key into Pageant, follow these steps.

In the Windows system tray, double-click the Pageant icon. Pagent Icon
right click Choose Add Key.
Select the .ppk file that you downloaded when you created the vockey2 key pair.  
Your screen should look similar to the following example.

Screen capture of Pageant

27. Add the first vockey that you downloaded earlier. The filename was labuser.*.

You should now have two keys listed. You can close the Pageant window.

In PuTTY, under Connection > SSH > Auth, select Allow agent forwarding. Expand  Auth and choose Credentials. Under Private key file for authentication choose Browse. Browse to the `labsuser.ppk` file that you downloaded, select it, and choose Open. Choose Accept. After you have completed this step, continue on to Task 9, step 32. Proceed to connect to the bastion host using PuTTY as you normally would, but don't open a .ppk file.

macOS or Linux users only
For macOS users, ssh-agent is already installed as part of the OS. To add your keys, complete the following steps.

Add your private keys to the keychain application by using the ssh-add command, with the -K option and the .pem file for the key. The command should look like the following example.

ssh-add -K vockey2.pem
Make sure that you add both the vockey.pem and vockey2.pem keys that you downloaded.

By adding the key to the agent, you can use SSH to connect to an instance without using the –i  option when you connect.

To verify that the keys are available to ssh-agent, use the ssh-add command with the -L option, like the following example.

ssh-add –L
The agent should display the keys that it's stored.

After the key is added to your keychain, you can connect to the bastion host instance with SSH by using the –A option. This option enables SSH agent forwarding. It also allows the local SSH agent to respond to a public key challenge when you use SSH to connect from the bastion host to a target instance in your VPC.

For example, to connect to an instance in a private subnet, you would enter the following command (this command enables SSH agent forwarding by using the bastion host instance):

ssh –A ec2-user@<bastion-IP-address-or-DNS-entry>
After you’re connected to the bastion host instance, you can use SSH to connect to a specific instance by entering a command like this example.

ssh user@<instance-IP-address-or-DNS-entry>
   
> Note:  The ssh-agent doesn't know which key it should use for a given SSH connection. Therefore, ssh-agent will sequentially try all the keys that are loaded in the agent. Because instances terminate the connection after five failed connection attempts, make sure that the agent has five or fewer keys. Because each administrator should have only a single key, this is usually not a problem for most deployments. For details about how to manage the keys in ssh-agent, use the man ssh-agent command.


## Task 9: Testing the SSH connection from the bastion host
In this task, you will test the SSH connection from your bastion host to the EC2 instance that is running in the Private Subnet.

32. Connect to the bastion host instance by using SSH.

**Tip**: Use the connection method that was described in the SSH passthrough section.

33. Connect to the private instance by using SSH and the IP address for the private instance.
```
ssh ec2-user@<private-ip-address-of-instance-in-private-subnet>
```
Now that you are connected to the EC2 instance in the Private Subnet, test its connection to the internet.
```
ping 8.8.8.8
```
**Tip**: Press CTRL+C to exit the command (from the Private EC2 instance)


You have now established communication between the Bastion Host in the Public Subnet and the EC2 instance in the Private Subnet, like in the following diagram:

![alt text](https://github.com/islamicity24/AmazonCity/raw/main/module-6-challenge-lab-bastion-to-private.png)
   
bastion host to private EC2 instance


**Architecture best practice**

In this first challenge, you implemented the architectural best practice of enable people to perform actions at a distance.

Expand here to learn more about it.
   According to the Well-Architected Framework, compute resources require multiple layers of defense to help protect them from external and internal threats. In practice, you should remove the ability for interactive access to reduce the risk of human error and the potential for manual configuration or management. The Well-Architected Framework recommends that you use a change management workflow to deploy EC2 instances by using infrastructure as code. Then, you should use tools, such as Amazon EC2 Systems Manager, to manage EC2 instances instead of allowing direct access or a bastion host. For more information about replacing a bastion host with Amazon EC2 Systems Manager, see this AWS Security Blog post

# New business requirement: Enhancing the security layer for private resources (Challenge #2)
Sofía and Nikhil are proud of the changes they made to the cafe's application architecture. They are pleased by the additional security they built, and they are also glad to have `a test environment` instance (for development) that they can use before they deploy  updates to `the production instance (Private E2 Instance)`. They tell Mateo about their new application architecture, and he's impressed! To further improve their application security, Mateo advises them to build `an additional layer of security by using custom network access control lists (network ACLs)`.

In this challenge, you will continue to take on the role of one of the café's system administrators. Now that you established secure access from the bastion host to the EC2 instance in the private subnet, you must enhance the security layer of the private subnet. To accomplish this task, you will create and configure a custom network ACL.


## Task 10: Creating a network ACL
In this task, you will create a custom network ACL to control traffic to and from the Private Subnet.

You can use network ACLs to control traffic between subnets. It's a good practice to use network ACLs to implement rules that are similar to your security group rules. The network ACLs provide an additional layer of protection.

For this challenge, you will create an EC2 instance in the Public Subnet. You will create a security group that allows Internet Control Message Protocol (ICMP) traffic from the local network. Next, you will create and configure your custom network ACL to deny ICMP traffic between the Private Subnet and this test instance. ICMP is used by the ping utility.

   To create a custom network ACL and configure it to allow all traffic to go into and out of the Private Subnet, you can use the following AWS CLI commands:

Create a custom network ACL called "Lab Network ACL":
```
aws ec2 create-network-acl --vpc-id <vpc-id> --tag-specifications 'ResourceType=network-acl,Tags=[{Key=Name,Value="Lab Network ACL"}]'
```   
35. Go to the Amazon VPC console, and inspect the default network ACL of Lab VPC.  

**Note 1**: The subnets that you created are automatically associated with the default network ACL.<br>                                   
   **Note 2**: The inbound and outbound rules of the default network ACL allow all traffic.

36. Create a custom network ACL called Lab Network ACL for the Lab VPC.  

   **Note**: The default inbound and outbound rules of the custom network ACL `deny all traffic`.

37. Configure your custom network ACL to allow ALL traffic that goes into and out of the Private Subnet.
Configure the custom network ACL to allow all traffic that goes into and out of the Private Subnet:
```
aws ec2 create-network-acl-entry --network-acl-id <network-acl-id> --rule-number 100 --protocol all --rule-action allow --cidr-block 10.0.1.0/24 --egress false   
aws ec2 create-network-acl-entry --network-acl-id <network-acl-id> --rule-number 200 --protocol all --rule-action allow --cidr-block 10.0.1.0/24 --egress true
```   
false = allow  and true = deny (default)
   These commands create a custom network ACL called "Lab Network ACL" for your VPC and configure it to allow all traffic that goes into and out of the Private Subnet.
   
   
Question 1: What is the purpose of the internet gateway in the public subnet?
- A. Allows instances in the private subnet to obtain a public IP address
- B. Allows instances in the public subnet to obtain a public IP addresss
- C. Allows instances in the public subnet with a public IP address to communicate with the internet
- D. Allows instances in the private subnet with a public IP address to communicate with the internet
   <br>
Which one the answer
The correct answer is C. The purpose of the internet gateway in the public subnet is to allow instances in the public subnet with a public IP address to communicate with the internet. This enables the instances to send and receive traffic to and from the internet, and allows them to be accessed by users or other resources outside of the AWS infrastructure.


Question 2: What allows the instance in the private subnet to connect to the internet so that it can download updates?
- A. The internet gateway in the public subnet
- B. The NAT gateway
- C. The Elastic IP address
- D .The default network ACL    
   
   so which one the right answer ?
<br>
   The correct answer is B. The NAT (Network Address Translation) gateway allows the instance in the private subnet to connect to the internet so that it can download updates. The NAT gateway provides a way for instances in the private subnet to access the internet while keeping them private and secure. It allows outgoing traffic from the private subnet to the internet, while also masking the private IP addresses of the instances behind the public IP address of the NAT gateway. This helps to prevent any direct communication between the internet and the instances in the private subnet, adding an additional layer of security to the infrastructure.

Option A is incorrect as the internet gateway in the public subnet allows instances in the public subnet with a public IP address to communicate with the internet, not the instances in the private subnet.

Option C is incorrect as an Elastic IP address is a static, public IPv4 address that can be assigned to an instance in a VPC, but it alone does not provide internet connectivity to the instance.

Option D is incorrect as the default network ACL controls inbound and outbound traffic for a subnet, but it does not provide internet connectivity to the instances in the private subnet.


Question 3: Can the instance in the private subnet be accessed directly from the internet?
- A. Yes
- B. No
<br>
   The answer is B. No, the instance in the private subnet cannot be accessed directly from the internet.

Instances in a private subnet are not assigned a public IP address and are not directly accessible from the internet. To access resources in a private subnet from the internet, you must use a bastion host, VPN connection or a NAT gateway to allow traffic to flow from the private subnet to the internet or vice versa.

The use of private subnets adds an additional layer of security to your infrastructure by keeping sensitive resources isolated from the internet and only allowing access to them through controlled channels.


Question 4: Why do you use two different key pairs to access the private instance and the bastion host?
- A. Each instance needs a different key pair
- B. It provided practice with creating key pairs
- C. Separate key pairs could help reduce the impact of a compromised bastion host
- D .Key pairs can't be reused
<br>
   The correct answer is C. Using two different key pairs to access the private instance and the bastion host can help reduce the impact of a compromised bastion host.

A bastion host is a server that is placed in a public subnet and is used as a gateway to access resources in private subnets. By using a bastion host, you can restrict access to resources in the private subnet from the public internet, and allow access only to authorized users who have access to the bastion host.

If an attacker gains access to the bastion host, they could potentially use it to access resources in the private subnet. However, by using separate key pairs for accessing the bastion host and the private instance, you can reduce the impact of a compromised bastion host. If the attacker gains access to the bastion host, they would not be able to access the private instance without also having the private instance key pair.

Option A is incorrect because multiple instances can use the same key pair.

Option B is incorrect because using separate key pairs is not necessarily about providing practice with creating them.

Option D is incorrect because key pairs can be reused, but it is not recommended for security reasons.


Question 5: Can the bastion host use ping and get a reply from the instance in the private subnet?
- Yes
- No
<br>
   The answer is dependent on the network configuration and security group rules set up in the VPC.

In general, if the bastion host and the instance in the private subnet are in the same VPC and the appropriate security group rules are in place, then the bastion host should be able to ping and get a reply from the instance in the private subnet.

However, if the security group rules are not configured properly, or if the instance in the private subnet is not set up to respond to ping requests, then the bastion host may not be able to ping the private instance.

So the answer is: It depends on the network configuration and security group rules set up in the VPC.


Question 6: Which security group rules allow the private EC2 instance to receive the return traffic when it pings the test instance?
- A. Outbound on private and outbound on test
- B  Outbound on private and inbound on test
- C  Inbound on private and outbound on test
- D  Inbound on private and inbound on test
<br>
The correct answer is B.

When the private EC2 instance pings the test instance, the ping request goes out from the private instance to the test instance, and then the test instance sends a reply back to the private instance. Therefore, the private instance needs to have outbound traffic allowed, and the test instance needs to have inbound traffic allowed, in order for the ping to work.

In specific, the security group rules that would allow the private EC2 instance to receive the return traffic when it pings the test instance are:

Outbound traffic allowed on the private instance security group to allow the ping request to go out from the private instance to the test instance.
Inbound traffic allowed on the test instance security group to allow the ping reply to come back from the test instance to the private instance.
Option A is incorrect because allowing outbound traffic on the test instance security group alone would not allow the private instance to receive the return traffic.

Option C is incorrect because allowing inbound traffic on the private instance security group alone would not allow the test instance to receive the initial ping request.

Option D is incorrect because allowing inbound traffic on both security groups would allow the private instance to receive the initial ping request from the test instance, but it would not allow the private instance to receive the return traffic.: If you get stuck, refer to the AWS Documentation.
   
   1. Create the custom network ACL:
```
   aws ec2 create-network-acl --vpc-id <vpc-id> --tag-specifications 'ResourceType=network-acl,Tags=[{Key=Name,Value=Lab Network ACL}]'
```
Make sure to replace `<vpc-id>` with the ID of your Lab VPC.
   
   2. Replace the default inbound and outbound rules of the custom network ACL to `deny all traffic`:   
```
   aws ec2 replace-network-acl-entries --network-acl-id <network-acl-id> --ingress '[{"RuleNumber":100,"Protocol":-1,"RuleAction":"deny","CidrBlock":"0.0.0.0/0"}]' --egress '[{"RuleNumber":100,"Protocol":-1,"RuleAction":"deny","CidrBlock":"0.0.0.0/0"}]'
```
   Make sure to replace <network-acl-id> with the ID of the custom network ACL.
   
   3. Create inbound and outbound rules for the custom network ACL to allow all traffic into and out of the Private Subnet:
```
   aws ec2 create-network-acl-entry --network-acl-id <network-acl-id> --rule-number 110 --protocol -1 --rule-action allow --cidr-block <private-subnet-cidr-block> --egress false
   aws ec2 create-network-acl-entry --network-acl-id <network-acl-id> --rule-number 110 --protocol -1 --rule-action allow --cidr-block <private-subnet-cidr-block> --egress true
```
   
## Task 11: Testing your custom network ACL
38. Create an EC2 instance in the Public Subnet of the Lab VPC (Public Test E2 Instance). It should meet the following criteria.
   - **AMI**: Amazon Linux 2 AMI (HVM)
   - **Instance type**: t2.micro
   - **Name**: `**Test Instance**`
   - Allows All ICMP – IPv4 inbound traffic to the instance through the security group

39. Note the private IP address of the Test Instance.
   
40. Test that you can reach the private IP address of the Test Instance from the Private Instance. From the Private Instance terminal window, run the following ping command:
```
ping <private-ip-address-of-test-instance>
```
   
41. Leave the ping utility running.

42. Modify your custom network ACL to `deny` All ICMP – IPv4 traffic to the <private-ip-address-of-test-instance>/32

   - Make sure to add /32 to the end of the private IP address.
   - Make sure that this rule is evaluated **first**.

43. **In the Private EC2 Instance terminal window**, the ping command should stop responding. The traffic to the Test Instance has been blocked.


You have now denied traffic from the Private Subnet to the Test Instance, as shown in the following diagram:

![Private EC2 instance to Test EC2 instance](https://github.com/islamicity24/AmazonCity/blob/main/module-6-challenge-lab-private-to-test.png)

private EC2 instance to test EC2 instance





**Architecture best practice**

In this second challenge, you protected your network resources by implementing the architectural best practice of controlling traffic at all layers.

Expand here to learn more about it.

**Answering questions about the lab**
Answers will be recorded when you choose the blue Submit button at the end of the lab.

Access the questions in this lab.
Choose the Details 
menu, and choose Show.
Choose the Access the multiple choice questions link that appears at the bottom of the page.
45. In the page that you loaded, answer the following questions:

   - Question 1: What is the purpose of the internet gateway in the public subnet?
   - Question 2: What allows the instance in the private subnet to connect to the internet so that it can download updates?
   - Question 3: Can the instance in the private subnet be accessed directly from the internet?
   - Question 4: Why do you use two different key pairs to access the private instance and the bastion host?
   - Question 5: Can the bastion host use ping and get a reply from the instance in the private subnet?
   - Question 6: Which security group rules allow the private EC2 instance to receive the return traffic when it pings the test instance?
   
```
   aws ec2 run-instances \
    --image-id ami-0c94855ba95c71c99 \
    --instance-type t2.micro \
    --subnet-id <public-subnet-id> \
    --key-name vockey1 \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Public Test E2 Instance}]' \
    --security-group-ids <public-test-instance-security-group-id> \
    --count 1
```
   **Note**: Replace `<public-subnet-id>` with the actual ID of the Public Subnet, and <public-test-instance-security-group-id>` with the actual ID of the security group that allows All ICMP – IPv4 inbound traffic to the instance.
   <br>
   <br>
   To get the private IP address of the Test Instance, you can use the following AWS CLI command:
```
   aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=Public Test E2 Instance" \
    --query "Reservations[*].Instances[*].PrivateIpAddress" \
    --output text
```
   
   Then, to modify the custom network ACL to deny All ICMP – IPv4 traffic to the `<private-ip-address-of-test-instance>/32`, you can use the following AWS CLI command:
```
   aws ec2 replace-network-acl-entry \
    --network-acl-id <custom-network-acl-id> \
    --rule-number 100 \
    --protocol 1 \
    --rule-action deny \
    --cidr-block <private-ip-address-of-test-instance>/32 \
    --egress false
```
   Note: Replace `<custom-network-acl-id>` with the actual ID of the custom network ACL. Also, make sure that the --rule-number is lower than any existing rule number in the network ACL, so that this new rule is evaluated first.

   
   
   
   
======== 
   
To create a custom network ACL in AWS CLI, follow these steps:

1. Create a custom network ACL for the Lab VPC:
```
aws ec2 create-network-acl --vpc-id <vpc-id> --tag-specifications 'ResourceType=network-acl,Tags=[{Key=Name,Value=Lab Network ACL}]'
```   
Replace `<vpc-id>` with the ID of the Lab VPC.

2. Create a rule to `allow` all inbound and outbound traffic for the Private Subnet:
```
aws ec2 create-network-acl-entry --network-acl-id <acl-id> --rule-number 100 --protocol -1 --rule-action allow --cidr-block <private-subnet-cidr-block> --egress false
```   
Replace `<acl-id>` with the ID of the custom network ACL, and `<private-subnet-cidr-block>` with the CIDR block of the Private Subnet.

3. Modify the rule to `deny` ICMP traffic to the Test Instance:
```
aws ec2 create-network-acl-entry --network-acl-id <acl-id> --rule-number 1 --protocol 1 --rule-action deny --cidr-block <private-ip-address-of-test-instance>/32 --egress false
```   
Replace `<acl-id>` with the ID of the custom network ACL, and `<private-ip-address-of-test-instance>` with the private IP address of the Test Instance.

4. To create the EC2 instance in the Public Subnet with the specified criteria, use the following command:

```
aws ec2 run-instances --image-id ami-0c94855ba95c71c99 --count 1 --instance-type t2.micro --subnet-id <public-subnet-id> --security-group-ids <security-group-id> --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Test Instance}]'
```   
Replace `<public-subnet-id>` with the ID of the Public Subnet, and `<security-group-id>` with the ID of the security group that `allows` All ICMP – IPv4 inbound traffic to the instance. Once the instance is running, note its private IP address and use it in the ping command to test the connectivity.
```
ping <private-ip-address-of-test-instance>
```



   
   
Question 1: What is the purpose of the internet gateway in the public subnet?
- A. Allows instances in the private subnet to obtain a public IP address
- B. Allows instances in the public subnet to obtain a public IP addresss
- C. Allows instances in the public subnet with a public IP address to communicate with the internet
- D. Allows instances in the private subnet with a public IP address to communicate with the internet
Which one the answer
The correct answer is C. The purpose of the internet gateway in the public subnet is to allow instances in the public subnet with a public IP address to communicate with the internet. This enables the instances to send and receive traffic to and from the internet, and allows them to be accessed by users or other resources outside of the AWS infrastructure.


Question 2: What allows the instance in the private subnet to connect to the internet so that it can download updates?
- A. The internet gateway in the public subnet
- B. The NAT gateway
- C. The Elastic IP address
- D .The default network ACL    so which one the right answer ?
The correct answer is B. The NAT (Network Address Translation) gateway allows the instance in the private subnet to connect to the internet so that it can download updates. The NAT gateway provides a way for instances in the private subnet to access the internet while keeping them private and secure. It allows outgoing traffic from the private subnet to the internet, while also masking the private IP addresses of the instances behind the public IP address of the NAT gateway. This helps to prevent any direct communication between the internet and the instances in the private subnet, adding an additional layer of security to the infrastructure.

Option A is incorrect as the internet gateway in the public subnet allows instances in the public subnet with a public IP address to communicate with the internet, not the instances in the private subnet.

Option C is incorrect as an Elastic IP address is a static, public IPv4 address that can be assigned to an instance in a VPC, but it alone does not provide internet connectivity to the instance.

Option D is incorrect as the default network ACL controls inbound and outbound traffic for a subnet, but it does not provide internet connectivity to the instances in the private subnet.


Question 3: Can the instance in the private subnet be accessed directly from the internet?
- A. Yes
- B. No
The answer is B. No, the instance in the private subnet cannot be accessed directly from the internet.

Instances in a private subnet are not assigned a public IP address and are not directly accessible from the internet. To access resources in a private subnet from the internet, you must use a bastion host, VPN connection or a NAT gateway to allow traffic to flow from the private subnet to the internet or vice versa.

The use of private subnets adds an additional layer of security to your infrastructure by keeping sensitive resources isolated from the internet and only allowing access to them through controlled channels.


Question 4: Why do you use two different key pairs to access the private instance and the bastion host?
- A. Each instance needs a different key pair
- B. It provided practice with creating key pairs
- C. Separate key pairs could help reduce the impact of a compromised bastion host
- D .Key pairs can't be reused

The correct answer is C. Using two different key pairs to access the private instance and the bastion host can help reduce the impact of a compromised bastion host.

A bastion host is a server that is placed in a public subnet and is used as a gateway to access resources in private subnets. By using a bastion host, you can restrict access to resources in the private subnet from the public internet, and allow access only to authorized users who have access to the bastion host.

If an attacker gains access to the bastion host, they could potentially use it to access resources in the private subnet. However, by using separate key pairs for accessing the bastion host and the private instance, you can reduce the impact of a compromised bastion host. If the attacker gains access to the bastion host, they would not be able to access the private instance without also having the private instance key pair.

Option A is incorrect because multiple instances can use the same key pair.

Option B is incorrect because using separate key pairs is not necessarily about providing practice with creating them.

Option D is incorrect because key pairs can be reused, but it is not recommended for security reasons.


Question 5: Can the bastion host use ping and get a reply from the instance in the private subnet?
- Yes
- No
The answer is dependent on the network configuration and security group rules set up in the VPC.

In general, if the bastion host and the instance in the private subnet are in the same VPC and the appropriate security group rules are in place, then the bastion host should be able to ping and get a reply from the instance in the private subnet.

However, if the security group rules are not configured properly, or if the instance in the private subnet is not set up to respond to ping requests, then the bastion host may not be able to ping the private instance.

So the answer is: It depends on the network configuration and security group rules set up in the VPC.


Question 6: Which security group rules allow the private EC2 instance to receive the return traffic when it pings the test instance?
- A. Outbound on private and outbound on test
- B  Outbound on private and inbound on test
- C  Inbound on private and outbound on test
- D  Inbound on private and inbound on test

The correct answer is B.

When the private EC2 instance pings the test instance, the ping request goes out from the private instance to the test instance, and then the test instance sends a reply back to the private instance. Therefore, the private instance needs to have outbound traffic allowed, and the test instance needs to have inbound traffic allowed, in order for the ping to work.

In specific, the security group rules that would allow the private EC2 instance to receive the return traffic when it pings the test instance are:

Outbound traffic allowed on the private instance security group to allow the ping request to go out from the private instance to the test instance.
Inbound traffic allowed on the test instance security group to allow the ping reply to come back from the test instance to the private instance.
Option A is incorrect because allowing outbound traffic on the test instance security group alone would not allow the private instance to receive the return traffic.

Option C is incorrect because allowing inbound traffic on the private instance security group alone would not allow the test instance to receive the initial ping request.

Option D is incorrect because allowing inbound traffic on both security groups would allow the private instance to receive the initial ping request from the test instance, but it would not allow the private instance to receive the return traffic.
   
**Submitting your work**
At the top of these instructions, choose `Submit` to record your progress and when prompted, choose Yes.

If the results don't display after a couple of minutes, return to the top of these instructions and choose Grades

 Tip: You can submit your work multiple times. After you change your work, choose Submit again. Your last submission is what will be recorded for this lab.

To find detailed feedback on your work, choose Details followed by  View Submission Report.

**Lab complete**
 Congratulations! You have completed the lab.   
