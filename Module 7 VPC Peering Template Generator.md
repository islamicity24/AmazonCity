# Build VPC Peering Command in CLI : 

## Module 7 - Guided Lab: Creating a VPC Peering Connection
Lab overview and objectives
You might want to connect your virtual private clouds (VPCs) when you must transfer data between them. This lab shows you how to create a private VPC peering connection between two VPCs.

After completing this lab, you should be able to:

Create a VPC peering connection
Configure route tables to use the VPC peering connection
 

At the end of this lab, your architecture will look like the following example:

Architecture


Duration
This lab will require approximately 20 minutes to complete.


## Task 1: Creating a VPC peering connection
Your task is to create a VPC peering connection between two VPCs.

A VPC peering connection is a one-to-one networking connection between two VPCs that enables you to route traffic between them privately. Instances in either VPC can communicate with each other like they are in the same network. You can create a VPC peering connection between your own VPCs, in a VPC in another AWS account, or with a VPC in a different AWS Region.

Two VPCs are provided as part of this lab: Lab VPC and Shared VPC. Lab VPC has an Inventory application that runs on an Amazon Elastic Compute Cloud (Amazon EC2) instance in a public subnet. Shared VPC has a database instance that runs in a private subnet.

 

Architecture

 

In the AWS Management Console, on the Services menu, choose VPC.

In the left navigation pane, choose Peering Connections.

* Choose **Create Peering Connection** and configure:

Peering connection name tag: Lab-Peer
VPC (Requester): Lab VPC
VPC (Accepter): Shared VPC
Choose Create Peering Connection then choose OK
When a VPC peering connection is created, the target VPC must accept the connection request. The target VPC must accept the request because it might be owned by a different account. Alternatively, the user that creates the peering connection might not have permission to accept the connection request for the target VPC. However, in this lab, you will accept the connection yourself.

Select  Lab-Peer.

Choose Actions then select Accept Request, and choose __Yes, Accept to accept the request.

In the pop-up box, choose _Close.


# Task 2: Configuring route tables
You will now update the route tables in both VPCs to send traffic from Lab VPC to the peering connection for Shared VPC.

Peering Connections

In the left navigation pane, choose __RouteTables.

Select  Lab Public Route Table (for Lab VPC).

You will configure the Public Route Table associated with Lab VPC. If the destination IP address falls in the range of Shared VPC, the Public Route Table will send traffic to the peering connection.

In the Routes tab, choose Edit routes then configure these settings:

Choose Add route
Destination: 10.5.0.0/16 (The setting is the Classless Inter-Domain Route, or CIDR, block range of Shared VPC.)
Target: Select Peering Connection, and then from the list, select Lab-Peer.
Choose Save routes then choose Close.
You will now configure the reverse flow for traffic that comes from Shared VPC and goes to Lab VPC.

Select  Shared-VPC Route Table. If the check boxes for any other route tables are selected, clear them.

This route table is for Shared VPC. You will now configure it to send traffic to the peering connection if the destination IP address falls in the range of Lab VPC.

In the Routes tab, choose Edit routes then configure these settings:

Choose Add route
Destination: 10.0.0.0/16 (This setting is the CIDR block range of Lab VPC.)
Target: Select Peering Connection, and then from the list, select Lab-Peer.
ChooseSave routes then choose Close.
The route tables are now configured to send traffic via the peering connection when the traffic is destined for the other VPC.


# Task 3: Testing the VPC peering connection
Now that you configured VPC peering, you will test the VPC peering connection. You will perform the test by configuring the Inventory application to access the database across the peering connection.

On the Services menu, choose EC2.

In the left navigation pane, choose Instances.

Copy the IPv4 Public IP address that is shown in the Description tab.

Open a new web browser tab with that IP address.

You should see the Inventory application and the following message: "Please configure settings to connect to database"

Choose  Settings and configure:

Endpoint: Paste the database endpoint. To find this endpoint, select Details. Next to AWS, choose Show. Then, copy the Endpoint.
Database: inventory
Username: admin
Password: lab-password
Choose Save
The application should now show data from the database.

This step confirms that the VPC peering connection was established because Shared VPC does not have an internet gateway. The only way to access the database is through the VPC peering connection.
To create a VPC peering connection and configure route tables to use the VPC peering connection using the CLI, follow these steps:

Open the terminal or command prompt on your local machine.

Use the following command to create a VPC peering connection:

```aws
aws ec2 create-vpc-peering-connection --vpc-id [vpc-id-of-requester] --peer-vpc-id [vpc-id-of-accepter] --peer-region [region-of-accepter] --peer-owner-id [AWS-account-id-of-accepter] --tags Key=Name,Value=[name-of-peering-connection]
```
Note: Replace [vpc-id-of-requester] with the ID of the requester VPC, [vpc-id-of-accepter] with the ID of the accepter VPC, [region-of-accepter] with the region of the accepter VPC, [AWS-account-id-of-accepter] with the AWS account ID of the accepter VPC, and [name-of-peering-connection] with the name of the VPC peering connection.

-----For example:


```aws
aws ec2 create-vpc-peering-connection --vpc-id vpc-0123456789abcdef0 --peer-vpc-id vpc-0123456789abcdef1 --peer-region us-west-2 --peer-owner-id 123456789012 --tags Key=Name,Value=Lab-Peer
```
Use the following command to accept the VPC peering connection:

```aws
aws ec2 accept-vpc-peering-connection --vpc-peering-connection-id [peering-connection-id]
```
Note: Replace [peering-connection-id] with the ID of the VPC peering connection.

For example:

```aws
aws ec2 accept-vpc-peering-connection --vpc-peering-connection-id pcx-0123456789abcdef0
```
Use the following command to modify the route tables for both VPCs to use the VPC peering connection:

```aws
aws ec2 create-route --route-table-id [route-table-id-of-requester] --destination-cidr-block [cidr-block-of-accepter-vpc] --vpc-peering-connection-id [peering-connection-id]
```
```aws
aws ec2 create-route --route-table-id [route-table-id-of-accepter] --destination-cidr-block [cidr-block-of-requester-vpc] --vpc-peering-connection-id [peering-connection-id]
```
Note: Replace [route-table-id-of-requester] with the ID of the route table of the requester VPC, [cidr-block-of-accepter-vpc] with the CIDR block of the accepter VPC, [peering-connection-id] with the ID of the VPC peering connection, [route-table-id-of-accepter] with the ID of the route table of the accepter VPC, and [cidr-block-of-requester-vpc] with the CIDR block of the requester VPC.

For example:

```aws
aws ec2 create-route --route-table-id rtb-0123456789abcdef0 --destination-cidr-block 10.5.0.0/16 --vpc-peering-connection-id pcx-0123456789abcdef0
```
```aws
aws ec2 create-route --route-table-id rtb-0123456789abcdef1 --destination-cidr-block 10.0.0.0/16 --vpc-peering-connection-id pcx-0123456789abcdef0
```
Verify that the VPC peering connection is working by testing connectivity between


AWS CLI:   
   Copy and paste the following into ~/.aws/credentials
[default]
aws_access_key_id=ASIASRF2WMBKVVYDZS2M
aws_secret_access_key=G8xr9NRYPgthjHO7O1U8LOrSSWaDyUscVCpZZ634
aws_session_token=FwoGZXIvYXdzECsaDM/fOq29zcNfqgolJiLLAbAvfTiIJC/Oed1TLOVWhsGLNVGmpebk7Ieu+G/drHAZIbVS3hUlV+W/F4wjzIKja1MplntRgwjBoUk8+ysTBTZ9Zo3YDMuqqoMTFb1XC5OHoknTA6aLVeH+RBwaw7g2EngA0zm6uT5SUdRO/JQxyG82SQFWs910Q9gYeONK5G1lTPBS2OepoRJ8F4oa+BtycQLJ+FX+CHyeHdcXC+nvHPe3NVUwOecKSfJgWz/AkcCgijzLa4uV/uXFRtVSAr/NuFk1Ijz2YeVhVFA8KN/2wKAGMi1300n/DkioaeyxS4AO0ThFQrU0cFnW1HpsOHfXkybyqGwtellI2gF1aa9w15U=

Cloud Labs
   Remaining session time: 02:18:04(139 minutes)
   Session started at: 2023-03-14T02:16:14-0700
   Session to end  at: 2023-03-14T05:16:14-0700

   Accumulated lab time: 00:41:00 (41 minutes)

   ips -- public:54.221.73.39, private:10.0.0.139

   SSH key  ShowDownload PEMDownload PPK
   AWS SSO  Download URL

SecretKey	OzSKo6bDLBWcpHkTBcZpZ0N0Hv62Uu6t4gDaBBbo

__Endpoint__	inventory-db.cmswnwy9hvjx.us-east-1.rds.amazonaws.com

AccessKey	AKIASRF2WMBKYJN4QJG2
