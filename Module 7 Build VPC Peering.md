# Build VPC Peering Command in CLI

## Module 7 - Guided Lab: Creating a VPC Peering Connection

### Lab Overview and Objectives

You might want to connect your virtual private clouds (VPCs) when you must transfer data between them. This lab shows you how to create a private VPC peering connection between two VPCs.

After completing this lab, you should be able to:

- Create a VPC peering connection
- Configure route tables to use the VPC peering connection

At the end of this lab, your architecture will look like the following example:

![Architecture](https://docs.aws.amazon.com/vpc/latest/peering/images/peering-diagram.png)

#### Duration

This lab will require approximately 20 minutes to complete.

### Task 1: Creating a VPC peering connection

Your task is to create a VPC peering connection between two VPCs.

A VPC peering connection is a one-to-one networking connection between two VPCs that enables you to route traffic between them privately. Instances in either VPC can communicate with each other like they are in the same network. You can create a VPC peering connection between your own VPCs, in a VPC in another AWS account, or with a VPC in a different AWS Region.

Two VPCs are provided as part of this lab: Lab VPC and Shared VPC. Lab VPC has an Inventory application that runs on an Amazon Elastic Compute Cloud (Amazon EC2) instance in a public subnet. Shared VPC has a database instance that runs in a private subnet.

#### Architecture

![Architecture](https://docs.aws.amazon.com/vpc/latest/peering/images/peering-diagram.png)

1. In the AWS Management Console, on the Services menu, choose VPC.
2. In the left navigation pane, choose Peering Connections.
3. Choose **Create Peering Connection** and configure:
   - Peering connection name tag: Lab-Peer
   - VPC (Requester): Lab VPC
   - VPC (Accepter): Shared VPC
4. Choose Create Peering Connection then choose OK
5. When a VPC peering connection is created, the target VPC must accept the connection request. The target VPC must accept the request because it might be owned by a different account. Alternatively, the user that creates the peering connection might not have permission to accept the connection request for the target VPC. However, in this lab, you will accept the connection yourself.
6. Select Lab-Peer.
7. Choose Actions then select Accept Request, and choose __Yes, Accept to accept the request.
8. In the pop-up box, choose _Close.

### Task 2: Configuring route tables

You will now update the route tables in both VPCs to send traffic from Lab VPC to the peering connection for Shared VPC.

#### Peering Connections

1. In the left navigation pane, choose **RouteTables.**
2. Select Lab Public Route Table (for Lab VPC).
3. You will configure the Public Route Table associated with Lab VPC. If the destination IP address falls in the range of Shared VPC, the Public Route Table will send traffic to the peering connection.
4. In the Routes tab, choose Edit routes then configure these settings:
   - Choose Add route
   - Destination: 10.5.0.0/16 (The setting is the Classless Inter-Domain Route, or CIDR, block range of Shared VPC.)
   - Target: Select Peering Connection, and then from the list, select Lab
   - Choose Save routes then choose Close.
You will now configure the reverse flow for traffic that comes from Shared VPC and goes to Lab VPC.

5. Select  Shared-VPC Route Table. If the check boxes for any other route tables are selected, clear them.

This route table is for Shared VPC. You will now configure it to send traffic to the peering connection if the destination IP address falls in the range of Lab VPC.

6. In the Routes tab, choose Edit routes then configure these settings:

Choose Add route
Destination: 10.0.0.0/16 (This setting is the CIDR block range of Lab VPC.)
Target: Select Peering Connection, and then from the list, select Lab-Peer.
ChooseSave routes then choose Close.
The route tables are now configured to send traffic via the peering connection when the traffic is destined for the other VPC.

### Task 3: Testing the VPC peering connection

1. Open the Amazon EC2 service from the AWS Management Console.

2. Navigate to the Instances tab on the left-hand side of the page.

3. Copy the IPv4 Public IP address that is shown in the Description tab.

4. Open a new web browser tab with that IP address.

5. You should see the Inventory application and the following message: "Please configure settings to connect to database"

6. Choose Settings and configure:

    - **Endpoint**: Paste the database endpoint. To find this endpoint, select Details. Next to AWS, choose Show. Then, copy the Endpoint.

    - Database: inventory

    - Username: admin

    - Password: lab-password

    - Choose Save.

7. The application should now show data from the database.

This step confirms that the VPC peering connection was established because Shared VPC does not have an internet gateway. The only way to access the database is through the VPC peering connection.

### Creating and Configuring a VPC Peering Connection via CLI

1. Open the terminal or command prompt on your local machine.

2. Use the following command to create a VPC peering connection:

``` aws
aws ec2 create-vpc-peering-connection --vpc-id [vpc-id-of-requester] --peer-vpc-id [vpc-id-of-accepter] --peer-region [region-of-accepter] --peer-owner-id [AWS-account-id-of-accepter] --tags Key=Name,Value=[name-of-peering-connection]
```

Note: Replace [vpc-id-of-requester] with the ID of the requester VPC, [vpc-id-of-accepter] with the ID of the accepter VPC, [region-of-accepter] with the region of the accepter VPC, [AWS-account-id-of-accepter] with the AWS account ID of the accepter VPC, and [name-of-peering-connection] with the name of the VPC peering connection.

For example:

```aws ec2 create-vpc-peering-connection --vpc-id vpc-0123456789abcdef0 --peer-vpc-id vpc-0123456789abcdef1 --peer-region us-west-2 --peer-owner-id 123456789012 --tags Key=Name,Value=Lab-Peer
```

3. Use the following command to accept the VPC peering connection:

``` aws cli
aws ec2 accept-vpc-peering-connection --vpc-peering-connection-id [peering-connection-id]
```

Note: Replace [peering-connection-id] with the ID of the VPC peering connection.

For example:

```
aws ec2 accept-vpc-peering-connection --vpc-peering-connection-id pcx-0123456789abcdef0
```

4. Use the following command to modify the route tables for both VPCs to use the VPC peering connection:

``` aws cli
aws ec2 create-route --route-table-id [route-table-id-of-requester] --destination-cidr-block [cidr-block-of-accepter-vpc] --vpc-peering-connection-id [peering-connection-id]
```

``` aws cli
aws ec2 create-route --route-table-id [route-table-id-of-accepter] --destination-cidr-block [cidr-block-of-requester-vpc] --vpc-pe
```

