# Module 9 - Challenge Lab: Creating a Scalable and Highly Available Environment for the Café
##  Scenario
The café will soon be featured in a famous TV food show. When it airs, Sofía and Nikhil anticipate that the café’s web server will experience a temporary spike in the number of users—perhaps even up to tens of thousands of users. Currently, the café’s web server is deployed in one Availability Zone, and they are worried that it won’t be able to handle the expected increase in traffic. They want to ensure that their customers have a great experience when they visit the website, and that they don’t experience any issues, such as lags or delays in placing orders.

To ensure this experience, the website must be **responsive**, able to **scale both up and down** to meet fluctuating customer demand, and be **highly available**. Instead of overloading a single server, the architecture must distribute customer order requests **across multiple application servers** so it can handle the increase in demand.

In this lab, you will take on the role of Sofía to implement a scalable and highly available architecture for the café's web application.


## Lab overview and objectives
In this lab, you use `Elastic Load Balancing` and Amazon `EC2 Auto Scaling` to create a **scalable and highly available environment** on AWS.

After completing this lab, you should be able to:
- Inspect a VPC ( must evaluate its current state )
- Update a network to work across multiple Availability Zones
- Create an Application Load Balancer
- Create a launch template
- Create an Auto Scaling group
- Test load balancing and automatic scaling

When you start the lab, your architecture will look like the following example:


starting architecture
![image](https://user-images.githubusercontent.com/126258837/236105992-2b47a8c8-cd11-4d8a-8763-965df410128e.png)


At the end of this lab, your architecture should look like the following example:


final architecture
![image](https://user-images.githubusercontent.com/126258837/236106107-db3ea64d-82c0-47a8-9596-e904ca0b0d4f.png)


Note: In this challenge lab, step-by-step instructions are not provided for most of the tasks. You must figure out how to complete the tasks on your own.


Duration
This lab will require approximately 90 minutes to complete.


##  AWS service restrictions
In this lab environment, access to AWS services and service actions might be restricted to the ones that are needed to complete the lab instructions. You might encounter errors if you attempt to access other services or perform actions beyond the ones that are described in this lab.


##  Accessing the AWS Management Console
At the top of these instructions, choose Start Lab to launch your lab.

A Start Lab panel opens, and it displays the lab status.

Tip: If you need more time to complete the lab, choose the Start Lab button again to restart the timer for the environment.

Wait until you see the message Lab status: ready, then close the Start Lab panel by choosing the X.

Note: This lab will take approximately 15 minutes to start.

At the top of these instructions, choose AWS.

This opens the AWS Management Console in a new browser tab. The system will automatically log you in.

Tip: If a new browser tab does not open, a banner or icon is usually at the top of your browser with a message that your browser is preventing the site from opening pop-up windows. Choose the banner or icon and then choose Allow pop ups.

Arrange the AWS Management Console tab so that it displays along side these instructions. Ideally, you will be able to see both browser tabs at the same time so that you can follow the lab steps more easily.


# A business request for the café: Implementing a scalable and highly available environment (Challenge)
Sofía understands that she must complete some tasks to implement high availability and scalability for the café’s web application. However, before changing the café’s application architecture, Sofía must evaluate its current state.

In the next several tasks, you will work as Sofía to create and configure the resources that you need to implement a scalable and highly available application.

## Task 1: Inspecting your environment
In this task, you will evaluate the current state of your lab environment.

5.  Explore the lab environment, including how the network is set up.
Tip: You might want to start in the Amazon VPC console.

To help you explore, continue to the next section and answer the questions about this lab.


## Answering questions about the lab
Answers will be recorded when you choose the blue Submit button at the end of the lab.

Access the questions in this lab.

Choose the Details  menu, and choose Show.
Choose the Access the multiple choice questions link that appears at the bottom of the page.
7.  In the page that you loaded, answer the questions:

-  Question 1: Which ports are open in the CafeSG security group?
-  Question 2: Can you connect from the internet to instances in Public Subnet 1?
-  Question 3: Should an instance in Private Subnet 1 be able to reach the internet?
-  Question 4: Should an instance in Private Subnet 2 be able to reach the internet?
-  Question 5: Can you connect to the CafeWebAppServer instance from the internet?
-  Question 6: What is the name of the Amazon Machine Image (AMI)?

## Task 2: Creating a NAT gateway for the second Availability Zone

To achieve high availability, the architecture must span at least two Availability Zones. However, before you launch Amazon Elastic Compute Cloud (Amazon EC2) instances for your web application servers in the second Availability Zone, you must create a NAT gateway for them. A NAT gateway will allow instances that do not have a public IP address to access the internet.

8.  Create a NAT gateway in the Public Subnet in the second Availability Zone.
```
aws ec2 create-nat-gateway --subnet-id <Public_Subnet_2_ID> --allocation-id <Elastic_IP_ID>
```

9.  Configure the network to send internet-bound traffic from instances in Private Subnet 2 to the NAT gateway you just created.
```
aws ec2 create-route --route-table-id <Private_Subnet_2_Route_Table_ID> --destination-cidr-block 0.0.0.0/0 --nat-gateway-id <NAT_Gateway_ID>
```
  1.  Open the Amazon VPC console at https://console.aws.amazon.com/vpc/.
  2.  In the navigation pane, choose "Nat Gateways".
  3.  Choose "Create NAT Gateway".
  4.  On the "Create a NAT gateway" page, choose the following configuration settings:
    - Subnet: Select the Public Subnet in the second Availability Zone.
    - Elastic IP allocation ID: Choose an existing Elastic IP address, or create a new one.
    - Click "Create NAT Gateway".
  5.  Wait for the NAT gateway to become available. This may take a few minutes.
  6.  Once the NAT gateway is available, note its ID and the ID of the Elastic IP address associated with it.
  7.  In the navigation pane, choose "Route Tables".
  8.  Select the route table that is associated with Private Subnet 2.
  9.  Choose the "Routes" tab, and then choose "Edit routes".
  10. In the "Edit routes" dialog box, choose "Add another route".
  11. In the "Destination" field, enter "0.0.0.0/0" to specify that this route applies to all internet-bound traffic.
  12. In the "Target" field, choose "Nat Gateway", and then select the NAT gateway that you created in step poin 4.
  13. Choose "Save routes".
  
You have now created a NAT gateway in the second Availability Zone and configured the network to send internet-bound traffic from instances in Private Subnet 2 to the NAT gateway.

## Task 3: Creating a bastion host instance in a public subnet
In this task, you will create a bastion host in a public subnet. In later tasks, you will create an EC2 instance in a private subnet and connect to it from this bastion host.

10. From the Amazon EC2 console, create an EC2 instance in one of the _public subnets_ of the _Lab VPC_. It must meet the following criteria:
```
aws ec2 run-instances --image-id ami-0dc2d3e4c0f9ebd18 --count 1 --instance-type t2.micro --key-name <YOUR_KEY_NAME> --security-group-ids <YOUR_SECURITY_GROUP_ID> --subnet-id <PUBLIC_SUBNET_ID> --associate-public-ip-address --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Bastion Host}]'
```
- Name: Bastion Host
- Amazon Machine Image (AMI): Amazon Linux 2 AMI (HVM)
- Instance type: t2.micro
- Auto-assign Public IP: This setting should be enabled (Network settings )
- Only allows the following traffic:
  - Type: SSH
  - Port: 22
  - Source: Your IP address (my IP)
  - Uses the **vockey** key pair

Wait for the instance to be created. You can check the status of the instance using the following command:
```
aws ec2 describe-instances --instance-ids <instance-id>
```

Allow only SSH traffic to the bastion host instance using the following command:
```
aws ec2 authorize-security-group-ingress --group-id <YOUR_SECURITY_GROUP_ID> --protocol tcp --port 22 --cidr <YOUR_IP_ADDRESS>/32
```

## Task 4: Creating a launch template (Create EC2 launch template) 

During the lab setup, an Amazon Machine Image (AMI) was created from the CafeWebAppServer instance. In this task, you will create a launch template by using this AMI.

11. Create a launch template by using the AMI that was created during lab setup. It must meet the following criteria.

  - AMI: Cafe WebServer Image
Tip: To locate the AMI, go to the the AMI dropdown menu and enter: Cafe

  - Instance type: t2.micro
Tip: To locate the instance type, go to the Instance Type dropdown menu and enter: t2
```
aws ec2 create-launch-template --launch-template-name my-launch-template --image-id ami-XXXX --instance-type t2.micro --key-name my-key-pair --security-group-ids sg-XXXX --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=webserver}]' --iam-instance-profile Arn=arn:aws:iam::123456789012:instance-profile/CafeRole
```
Replace ami-XXXX with the AMI ID of the Cafe WebServer Image, sg-XXXX with the security group ID of CafeSG, and arn:aws:iam::123456789012:instance-profile/CafeRole with the ARN of the CafeRole IAM instance profile. Also, replace my-launch-template and my-key-pair with your desired launch template name and key pair name, respectively.

![image](https://user-images.githubusercontent.com/126258837/236120006-69ba94ab-2153-4e6e-85cb-c9e28172a432.png)


![image](https://user-images.githubusercontent.com/126258837/236111144-3e4bb430-5d0d-42e7-b33d-0affa41d1c9c.png)

Key pair (login): Uses a new key pair Tip: Create a new key pair and select it. Make sure that you download the key pair to your local computer.

Security groups: CafeSG
Tip: To locate the security group, go to the Security groups dropdown menu and enter: CafeSG

Resource tags:

Key: Name
Value: webserver
Resource types: Instances
IAM Instance Profile: CafeRole
Tip: Look in Advanced Details for this setting.



## Task 5: Creating an (EC2) Auto Scaling group 
Now that the launch template is defined, you will create an Auto Scaling group for the instances. In this task, do not create a load balancer when you create the Auto Scaling group. (You will create a load balancer in the next task.)

12. Create a new Auto Scaling Group that meets the following criteria:
```
aws autoscaling create-auto-scaling-group --auto-scaling-group-name my-auto-scaling-group --launch-template LaunchTemplateName=my-launch-template --vpc-zone-identifier "subnet-XXXX,subnet-YYYY" --min-size 2 --max-size 6 --desired-capacity 2 --target-tracking-configuration "MetricType=CPUUtilization,TargetValue=25,PredefinedMetricSpecification={PredefinedMetricType=ASGAverageCPUUtilization},ScaleInCooldown=60,ScaleOutCooldown=60"
```
Replace subnet-XXXX and subnet-YYYY with the IDs of Private Subnet 1 and Private Subnet 2, respectively. Also, replace my-auto-scaling-group with your desired Auto Scaling group name.

![image](https://user-images.githubusercontent.com/126258837/236262242-240a8fcf-cd20-41a4-8deb-5ebeedd59d73.png)

- Step 1  Launch template: Uses the launch template that you created in the previous task

- Step 2  VPC: Uses the VPC that was configured for this lab

- Subnets: Uses Private Subnet 1 and Private Subnet 2
![image](https://user-images.githubusercontent.com/126258837/236262689-695b654b-7277-49f3-80fb-8b7316565ec0.png)

- Step 3  Skips all the advanced options

- Step 4  Has a Group size configured as:

  - Desired capacity: 2 
  - Minimum capacity: 2 
  - Maximum capacity: 6
- Enables the Target tracking scaling policy configured as:
  - Metric type: Average CPU utilization
  - Target Value: 25
  - Instances need: 60

![image](https://user-images.githubusercontent.com/126258837/236264073-5766beb7-880b-4f7b-8520-f9daa9176105.png)

13. To verify that you created the Auto Scaling group correctly, go to the Amazon EC2 console. You should have two instances, both with the name that you configured as resource tags in the previous task.

![image](https://user-images.githubusercontent.com/126258837/236265202-260155d2-c014-4536-b67a-cdddf499be8b.png)


## Task 6: Creating a load balancer
Now that you web application server instances are deployed in private subnets, you need a way for the outside world to connect to them. In this task, you will create a load balancer to distribute traffic across your private instances.
```
aws ec2 create-security-group --group-name CafeLBSecurityGroup --description "Cafe load balancer security group" --vpc-id <vpc-id>

aws ec2 authorize-security-group-ingress --group-name CafeLBSecurityGroup --protocol tcp --port 80 --cidr 0.0.0.0/0
```

```
aws elbv2 create-target-group --name CafeTargetGroup --protocol HTTP --port 80 --vpc-id <vpc-id> --health-check-path /cafe
```

14. Create an HTTP Application Load Balancer that meets the following criteria:
```
aws elbv2 create-load-balancer --name my-load-balancer --subnets subnet-AAAA subnet-BBBB --security-groups sg-CCCC --scheme internet-facing --type application
```
so ==>
```
aws elbv2 create-load-balancer --name CafeLoadBalancer --subnets <public-subnet-1> <public-subnet-2> --security-groups <security-group-id> --type application --scheme internet-facing
```
Replace subnet-AAAA and subnet-BBBB with the IDs of the two public subnets, and replace sg-CCCC with the ID of the security group that allows HTTP traffic from anywhere. Also, replace my-load-balancer with your desired load balancer name.
- VPC: Uses the VPC configured for this lab
- Subnets: Uses the two public subnets
- Skips the HTTPS security configuration settings
- Security group: Creates a new security group that allows HTTP traffic from anywhere

- Target group: Creates a new target group
- Skips registering targets
Note: Wait until the load balancer is active.
```
aws elbv2 create-target-group --name my-target-group --protocol HTTP --port 80 --vpc-id vpc-XXXX --health-check-protocol HTTP --health-check-path / --health-check-interval-seconds 30 --health-check-timeout-seconds 5 --healthy-threshold-count 2 --unhealthy-threshold-count 2
```
Replace vpc-XXXX with the ID of the VPC that was configured for this lab, and replace my-target-group with your desired target group name.

To register the Auto Scaling group as a target for the load balancer, use the following command:
```
aws autoscaling attach-load-balancers --auto-scaling-group-name my-auto-scaling-group --load-balancer-names my-load-balancer
```
Replace my-auto-scaling-group and my-load-balancer with your desired Auto Scaling group name and load balancer name, respectively.

Create a listener for the load balancer:

```
aws elbv2 create-listener --load-balancer-arn <load-balancer-arn> --protocol HTTP --port 80 --default-actions Type=forward,TargetGroupArn=<target-group-arn>
```

Replace <load-balancer-arn> with the ARN of the load balancer that you created in step 4. Replace <target-group-arn> with the ARN of the target group that you created in step 3.

15. Modify the Auto Scaling group that you created in the previous task by adding this new load balancer.

Hint: Add the target group you created in the Load Balancer configuration.

Modify the Auto Scaling group to add the new load balancer:
```
aws autoscaling attach-load-balancers --auto-scaling-group-name CafeASG --load-balancer-names CafeLoadBalancer
```
  
Replace CafeASG with the name of the Auto Scaling group that you created in the previous task.

Sofía has now created and configured the resources that the web application needs to be highly available and scalable. However, Sofía knows that she still has more work to do. To complete the process of updating the application architecture, Sofía must test the café’s web application to make sure that it performs as expected.

In the next tasks, you will continue working in the role of Sofía and test whether the café web application automatically scales under load.

  Wait for the load balancer to become active. You can check the status of the load balancer by running the following command:
```
aws elbv2 describe-load-balancers --load-balancer-arns <load-balancer-arn>
```
Replace <load-balancer-arn> with the ARN of the load balancer that you created in step 4.

## Task 7: Testing the web application
In this task, you will test the café web application.

16. To test the café web application, visit the Domain Name System (DNS) name of your load balancer and append /cafe to the URL.
  
For example, if your load balancer DNS name is CafeLoadBalancer-1234567890.us-west-2.elb.amazonaws.com, you should visit http://CafeLoadBalancer-1234567890.us-west-2.elb.amazonaws.com/cafe.

To test automatic scaling under load, use SSH to connect to one of the running web server instances and run the stress test commands. Watch the Amazon EC2 console to observe new instances being deployed.
  
The café application should load.

![image](https://user-images.githubusercontent.com/126258837/236274695-382b0c72-ba43-4c04-9a7f-d2ba065a36ee.png)

If it does not, go back through the lab tasks and check your work. Pay attention to the following resources:

- Network configuration: Did you add the NAT gateway correctly?
  ![image](https://user-images.githubusercontent.com/126258837/236276079-2db9d75b-9b8d-4b91-824f-8f371c4c07ed.png)

- Route tables: Did you update the route tables with the NAT gateway?
  ![image](https://user-images.githubusercontent.com/126258837/236276457-3a7f48f5-570e-484e-9f70-1bc54206c5de.png)

- Launch template: Does the instance specify an IAM role?
- Load balancer: Is the load balancer in the public subnets?
  ![image](https://user-images.githubusercontent.com/126258837/236276712-fddd8d72-b3d1-494e-a2f3-30d6cff2a320.png)

- Instances: Are the instances deployed from the Auto Scaling group that is in the correct subnets?
- Security groups: Do the security groups allow HTTP traffic from the internet?

## Task 8: Testing automatic scaling under load
In this task, you will test whether the café application scales out automatically.

17. By using Secure Shell (SSH) passthrough through the bastion host instance, use SSH to connect to one of the running web server instances.
Tip: You will need to modify the CafeSG security group to allow SSH traffic over port 22 from the bastion host.

```
  Connect to instanceInfo
Connect to your instance i-07a65f8c32d0ed651 (webserver) using any of these options


EC2 Instance Connect

Session Manager

SSH client

EC2 serial console
You may not be able to connect to this instance as ports 22 may need to be open in order to be accessible. The current associated security groups don't have ports 22 open.

Instance ID
i-07a65f8c32d0ed651 (webserver)
Open an SSH client.

Locate your private key file. The key used to launch this instance is cafekey.pem

Run this command, if necessary, to ensure your key is not publicly viewable.
 chmod 400 cafekey.pem

Connect to your instance using its Private IP:
 10.0.3.241

Example:
  **Dari Bastion Host Instance :**
 ssh -i "cafekey.pem" root@10.0.3.241
Note: In most cases, the guessed user name is correct. However, read your AMI usage instructions to check if the AMI owner has changed the default AMI user name.
```
  
18. From the web server instance, use the following commands to start a stress test. This test increases the load on the web server CPU:
```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum install stress -y
stress --cpu 1 --timeout 600
```

19. Verify that the Auto Scaling group deploys new instances.

Continue to observe the Amazon EC2 console.
During the test, you should observe that more web server instances are deployed.

## Update from the café
After Sofía finishes testing the café web application's performance, she tells her parents about the changes—when traffic increases, the café application now successfully scales out. Frank and Martha are impressed that Sofía implemented a highly available and scalable architecture for the application.

Meanwhile, the entire café team is excited! They are busy preparing for the increased volume of orders that they anticipate from being featured on the TV show. As they work on their preparations, they know that they can rely on automatic scaling to help them take orders and delight new customers.


Submitting your work
At the top of these instructions, choose Submit to record your progress and when prompted, choose Yes.

If the results don't display after a couple of minutes, return to the top of these instructions and choose Grades

Tip: You can submit your work multiple times. After you change your work, choose Submit again. Your last submission is what will be recorded for this lab.

To find detailed feedback on your work, choose Details followed by  View Submission Report.


Lab complete
 Congratulations! You have completed the lab.
