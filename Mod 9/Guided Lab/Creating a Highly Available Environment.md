# Module 9 – Guided Lab: Creating a Highly Available Environment
Lab overview and objectives
Critical business systems should be deployed as highly available applications—that is, applications remain operational even when some components fail. To achieve high availability in Amazon Web Services (AWS), we recommend that you run services across multiple Availability Zones.

Many AWS services are inherently highly available, such as load balancers. Many AWS services can also be configured for high availability, such as deploying Amazon Elastic Compute Cloud (Amazon EC2) instances in multiple Availability Zones.

In this lab, you will start with an application that runs on a single EC2 instance. You will then make the application highly available.

After completing this lab, you should be able to:

Inspect a provided virtual private cloud (VPC)
Create an Application Load Balancer
Create an Auto Scaling group
Test the application for high availability
 

At the end of this lab, your architecture will look like the following example:

Architecture
![image](https://user-images.githubusercontent.com/126258837/235858111-81096872-a1aa-4072-a234-42caa34beb36.png)


Duration
The lab requires approximately 40 minutes to complete.

 


AWS service restrictions
In this lab environment, access to AWS services and service actions might be restricted to the ones that are needed to complete the lab instructions. You might encounter errors if you attempt to access other services or perform actions beyond the ones that are described in this lab.


Accessing the AWS Management Console
At the top of these instructions, choose Start Lab to launch your lab.

A Start Lab panel opens, and it displays the lab status.

 Tip: If you need more time to complete the lab, restart the timer for the environment by choosing the Start Lab button again.

 

Wait until the Start Lab panel displays the message Lab status: ready, then close the panel by choosing the X.
 

At the top of these instructions, choose AWS.

This action opens the AWS Management Console in a new browser tab. The system automatically logs you in.

 Tip: If a new browser tab does not open, a banner or icon is usually at the top of your browser with the message that your browser is preventing the site from opening pop-up windows. Choose the banner or icon, and then choose Allow pop-ups.

 

Arrange the AWS Management Console tab so that it displays alongside these instructions. Ideally, you will have both browser tabs open at the same time so that you can follow the lab steps more easily.

 Do not change the Region unless specifically instructed to do so.


## Task 1: Inspecting your VPC
This lab begins with an environment that is already deployed via AWS CloudFormation. It includes:

- A VPC
- Public and private subnets in two Availability Zones
- An internet gateway (not shown) that is associated with the public subnets
- A Network Address Translation (NAT) gateway in one of the public subnets
- An Amazon Relational Database Service (Amazon RDS) instance in one of the private subnets
Task 1
![image](https://user-images.githubusercontent.com/126258837/235858594-1564cb6c-e05c-460b-a564-b766e9136829.png)

In this task, you will review the configuration of the VPC that was created for this lab.

 

5. On the AWS Management Console, on the Services menu, choose VPC.
 

6. In the left navigation pane, under Filter by VPC, choose the  Select a VPC box and select Lab VPC.

This setting will limit the console to only show resources that are associated with the Lab VPC.

 

7. In the left navigation pane, choose Your VPCs.

Here, you can access information about the Lab VPC that was created for you.

The IPv4 CIDR column has a value of 10.0.0.0/16, which means that this VPC includes all IP addresses that start with 10.0.x.x.

 

8. In the left navigation pane, choose Subnets.

Here, you can access information about Public Subnet 1:

The VPC column shows that this subnet exists inside of Lab VPC.
The IPv4 CIDR column has a value of 10.0.0.0/24, which means that this subnet includes the 256 IP addresses between 10.0.0.0 and 10.0.0.255. Five of these addresses are reserved and unusable.
The Availability Zone column lists the Availability Zone where this subnet resides.
 

9. To reveal more details at the bottom of the page, select  Public Subnet 1.

Tip: To adjust the size of the lower window pane, you can drag the divider.

 

10. In the lower half of the page, choose the Route table tab.

This tab includes details about the routing for this subnet:

The first entry specifies that traffic destined within the Classless Inter-Domain Routing (CIDR) range for the VPC (10.0.0.0/16) will be routed within the VPC (local).
The second entry specifies that any traffic destined for the internet (0.0.0.0/0) is routed to the internet gateway (igw-). This setting makes the subnet a public subnet.
 

11. Choose the Network ACL tab.

This tab has information about the network access control list (network ACL) that is associated with the subnet. The rules currently permit all traffic to flow in and out of the subnet, but the rules can be further restricted by using security groups.

 

12. In the left navigation pane, choose Internet Gateways.

Notice that an internet gateway is already associated with Lab VPC.

 

13. In the left navigation pane, choose Security Groups.
 

14. Select  Inventory-DB.

This security group controls incoming traffic to the database.

 

15. In the lower half of the page, choose the Inbound rules tab.

These rules permit inbound MySQL or Aurora traffic (port 3306) from anywhere in the VPC (10.0.0.0/16). You will later modify this setting so it only accepts traffic from the application servers.

 

16. Choose the Outbound rules tab.

By default, security groups allow all outbound traffic. However, this setting can be modified as needed.

 


## Task 2: Creating an Application Load Balancer
To build a highly available application, it is a best practice to launch resources in multiple Availability Zones. Availability Zones are physically separate data centers (or groups of data centers) in the same Region. If you run your applications across multiple Availability Zones, you can provide greater availability if a data center experiences a failure.

Because the application runs on multiple application servers, you will need a way to distribute traffic amongst those servers. You can accomplish this goal by using a load balancer. This load balancer will also perform health checks on instances and only send requests to healthy instances.

Task 2
![image](https://user-images.githubusercontent.com/126258837/235861147-c59cd4bc-dc4b-43e1-863b-a42b175a9931.png)

 

17. On the Services menu, choose EC2.
 

18. In the left navigation pane, choose Load Balancers (you might need to scroll down to find it).
 

19. Choose Create Load Balancer

Several types of load balancers are displayed. Read the descriptions of each type to understand their capabilities.

 

20. Under Application Load Balancer, choose Create.
 

21. For Load balancer name, enter: Inventory-LB
 

22. Scroll down to the Network mapping section, then for VPC, select Lab VPC.

Important: Be sure to choose Lab VPC. It is likely not the default selection.

You will now specify which subnets the load balancer should use. It will be a public load balancer, so you will select both public subnets.

 

23. Under Mappings, choose the first Availability Zone, then choose the Public Subnet that displays.
 

24. Choose the second Availability Zone, then choose the Public Subnet that displays.

You should now have selected two subnets: Public Subnet 1 and Public Subnet 2. (If not, go back and try the configuration again.)

 

25. In the Security groups section, select Create new security group, then configure:

Security group name: Inventory-LB
Description: Enable web access to load balancer
VPC: Remove the default VPC by choosing the X to the right of it. Then select  Lab VPC.
 

26. Under Inbound rules, choose Add rule and configure as described:

Type: HTTP
Source: Anywhere-IPv4
 

27. Still under Inbound rules, choose Add rule again and configure:

Type: HTTPS
Source: Anywhere-IPv4
 

28. Choose Create security group.
 

29. Assign the security group to the load balancer:

Return to the browser tab where you are still configuring the load balancer.
Scroll down to the Security groups area and choose the  refresh icon.
For Security groups, select the Inventory-LB security group.
Next, below the Security groups dropdown menu, select the X next to the default security group so that the Inventory-LB is now the only security group chosen.
 

30. In the Listeners and routing section, choose Create target group.

Analysis: Target groups define where to send traffic that comes into the load balancer. The Application Load Balancer can send traffic to multiple target groups based upon the URL of the incoming request, such as having requests from mobile apps going to a different set of servers. Your web application will use only one target group.

 

 

31. A new browser tab will open. Configure the target group as described here:

Choose a target type: Instances

Target group name: Inventory-App

VPC: Ensure that Lab VPC is chosen.

Scroll down and expand  Advanced health check settings.

Note: The Application Load Balancer automatically performs health checks on all instances to ensure that they are responding to requests. The default settings are recommended, but you will make them slightly faster for use in this lab.

Healthy threshold: 2

Interval: 10 (seconds)

This means that the health check will be performed every 10 seconds, and if the instance responds correctly twice in a row, it will be considered healthy.

Choose Next. The Register targets screen appears.

Note: Targets are the individual instances that will respond to requests from the load balancer.

You do not have any web application instances yet, so you can skip this step.

Review the settings and choose Create target group.

 

32. Return to the browser tab where you already started defining the load balancer.
 

33. In the Listeners and routing section, choose the  refresh icon.
 

34. For the Listener HTTP:80 row, set the Default action to forward to Inventory-app.
 

35. Scroll to the bottom of the page, and choose Create load balancer.

The load balancer is successfully created.
Choose View load balancer.
 
