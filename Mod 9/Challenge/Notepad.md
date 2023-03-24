
aws ec2 create-vpc --cidr-block <CIDR block> --region <region>
```
aws ec2 create-vpc --cidr-block 10.0.0.0/16 --region ap-southeast-3
```
PS C:\Users\LENOVO\source\repos\cafe.tamankuliner.com> aws ec2 create-vpc --cidr-block 10.0.0.0/16 --region ap-southeast-3
```
Vpc:
  CidrBlock: 10.0.0.0/16
  CidrBlockAssociationSet:
  - AssociationId: vpc-cidr-assoc-0d4832c68d68c04ad
    CidrBlock: 10.0.0.0/16
    CidrBlockState:
      State: associated
  DhcpOptionsId: dopt-0f7b058dd86901fd3
  InstanceTenancy: default
  Ipv6CidrBlockAssociationSet: []
  IsDefault: false
  OwnerId: '078802129687'
  State: pending
  VpcId: vpc-063fb4e46899c84f6
```
SALAH : aws ec2 create-subnet --vpc-id vpc-054528175021285f8 --cidr-block 10.0.0.0/24 --availability-zone ap-southeast-3a --map-public-ip-on-launch --region ap-southeast-3

aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block <cidr-block> --availability-zone <availability-zone> --tag-specifications 'ResourceType=subnet,Tags=[{Key=<key>,Value=<value>}]'
```
aws ec2 create-subnet --vpc-id vpc-063fb4e46899c84f6 --cidr-block 10.0.0.0/24 --availability-zone ap-southeast-3a --region ap-southeast-3 --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=PublicSubnet}]'
```
```  
Subnet:
  AssignIpv6AddressOnCreation: false
  AvailabilityZone: ap-southeast-3a
  AvailabilityZoneId: apse3-az1
  AvailableIpAddressCount: 251
  CidrBlock: 10.0.0.0/24
  DefaultForAz: false
  EnableDns64: false
  Ipv6CidrBlockAssociationSet: []
  Ipv6Native: false
  MapPublicIpOnLaunch: false
  OwnerId: '078802129687'
  PrivateDnsNameOptionsOnLaunch:
    EnableResourceNameDnsAAAARecord: false
    EnableResourceNameDnsARecord: false
    HostnameType: ip-name
  State: available
  SubnetArn: arn:aws:ec2:ap-southeast-3:078802129687:subnet/subnet-054167ac5e91c0f88
  SubnetId: subnet-054167ac5e91c0f88
  Tags:
  - Key: <key>
    Value: <value>
  VpcId: vpc-054528175021285f8
```

aws ec2 create-internet-gateway --region ap-southeast-3

aws ec2 create-nat-gateway --subnet-id <ID of Public Subnet 2> --allocation-id <ID of Elastic IP address> --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=MyNatGateway}]'
```  
aws ec2 create-nat-gateway --subnet-id subnet-054167ac5e91c0f88 --allocation-id eipalloc-0848b01fd26ef8b73 --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=TamKulNatGateway}]'
aws ec2 describe-nat-gateways
```

aws ec2 describe-instances

aws ec2 describe-vpc-attribute                   
describe-vpc-classic-link-dns-support    
describe-vpc-endpoint-connections      
describe-vpc-endpoint-service-permissions 
describe-vpc-endpoints                   
describe-vpcs                            
describe-vpc-classic-link
describe-vpc-endpoint-connection-notifications
describe-vpc-endpoint-service-configurations
describe-vpc-endpoint-services
describe-vpc-endpoint-services
```
aws ec2 describe-vpcs --vpc-id vpc-00c36eece05a78cb8
```  
PS C:\Users\LENOVO\source\repos\cafe.tamankuliner.com> aws ec2 describe-vpcs --vpc-id vpc-00c36eece05a78cb8 --region ap-southeast-3
```
Vpcs:
- CidrBlock: 172.31.0.0/16
  CidrBlockAssociationSet:
  - AssociationId: vpc-cidr-assoc-0fddeefb52818cd19
    CidrBlock: 172.31.0.0/16
    CidrBlockState:
      State: associated
  DhcpOptionsId: dopt-08e2429a08ae32a04
  InstanceTenancy: default
  IsDefault: true
  OwnerId: '078802129687'
  State: available
  VpcId: vpc-00c36eece05a78cb8
```
  
Some of the keywords related to NAT gateways are:

Network Address Translation (NAT)
Private subnet
Public subnet
Elastic IP address
Internet gateway
Transit gateway
Virtual private gateway
Source IP address
Pricing
Data transfer charges
Availability Zones
Interface endpoint
Gateway endpoint
AWS PrivateLink  

Jelaskan lebih detail dan akurat : Some of the keywords related to NAT gateways are:

Network Address Translation (NAT)
Private subnet
Public subnet
Elastic IP address
Internet gateway
Transit gateway
Virtual private gateway
Source IP address
Pricing
Data transfer charges
Availability Zones
Interface endpoint
Gateway endpoint
AWS PrivateLink  

**Network Address Translation (NAT)** is a process of modifying IP addresses in the packet headers of an IP packet. NAT is commonly used in networking to allow hosts on a private network to access the internet using a single public IP address.

**Private subnet** is a subset of a VPC's IP address range that is inaccessible from the internet. Instances launched in private subnets cannot have a public IP address assigned to them, and hence they cannot communicate with the internet directly.

**Public subnet** is a subset of a VPC's IP address range that is accessible from the internet. Instances launched in public subnets can have public IP addresses assigned to them, and they can communicate with the internet directly.

**Elastic IP address **is a static, public IPv4 address that you can allocate and associate with your AWS resources such as EC2 instances, NAT gateways, and load balancers. Elastic IP addresses are designed to be dynamically remappable to different instances, allowing you to quickly switch traffic between instances in response to system failures.

**Internet gateway** is a horizontally scalable, redundant, and highly available VPC component that allows communication between instances in a VPC and the internet.

**Transit gateway** is a fully managed service that enables customers to connect their VPCs and on-premises networks through a single gateway. It simplifies network architecture by centralizing connectivity and routing between multiple VPCs and on-premises networks.

**Virtual private gateway** is a virtual router that is the VPN endpoint on the AWS side of a VPN connection. It provides two VPN endpoints (tunnels) for automatic failover.

Source IP address refers to the IP address of the sender of a network packet. NAT gateways replace the source IP address of instances in private subnets with the IP address of the NAT gateway when forwarding traffic to the internet.

Pricing for NAT gateways is based on the availability of the NAT gateway and the amount of data processed by the gateway. Customers are charged hourly for each NAT gateway and for each GB of data processed by the gateway.

Data transfer charges for NAT gateways are the fees associated with data transfer across AWS regions and Availability Zones.

**Availability Zones** are distinct locations within a geographic region that are designed to be isolated from failures in other Availability Zones. AWS customers can deploy their resources across multiple Availability Zones for increased fault tolerance and high availability.

**Interface endpoint and gateway endpoint** are two types of endpoints for connecting to AWS services through a private network connection. They provide secure and scalable connectivity to AWS services, without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection.

**AWS PrivateLink** is a service that allows customers to access AWS services over a private endpoint in their VPCs, without using the internet. AWS PrivateLink provides a highly available and scalable architecture, allowing customers to securely access services within their VPCs.


![USE CASES}(https://docs.aws.amazon.com/vpc/latest/userguide/nat-gateway-scenarios.html)
