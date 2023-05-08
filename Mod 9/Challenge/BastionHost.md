How to create bastion host using best aws cli code step by step

Creating a bastion host on AWS using the AWS CLI can be done in the following steps:

1.  Launch an EC2 instance to be used as the bastion host. You can do this using the following command:
```
aws ec2 run-instances --image-id <ami-id> --count 1 --instance-type <instance-type> --key-name <key-pair-name> --security-group-ids <security-group-id> --subnet-id <subnet-id> --tag-specifications 'ResourceType=instance,Tags=[{Key=<tag-key>,Value=<tag-value>}]'
```
Replace `<ami-id>` with the ID of the Amazon Machine Image (AMI) you want to use for the bastion host, `<instance-type>` with the instance type you want to use, `<key-pair-name>` with the name of the key pair you want to use for SSH access, `<security-group-id>` with the ID of the security group you want to use for the bastion host, `<subnet-id>` with the ID of the subnet in which you want to launch the bastion host, `<tag-key>` with a key for identifying the instance, and `<tag-value>` with a value for the tag key.

2.  Assign an Elastic IP address to the bastion host. You can do this using the following command:
```
aws ec2 allocate-address --domain vpc
```  
This will return an Elastic IP address that you can associate with the bastion host.

3.  Associate the Elastic IP address with the bastion host. You can do this using the following command:
```
aws ec2 associate-address --instance-id <instance-id> --public-ip <public-ip>
```
Replace `<instance-id>` with the ID of the bastion host instance and `<public-ip>` with the Elastic IP address you allocated in step 2 (return).

4.  Update the security group for your other instances to allow SSH access from the bastion host. You can do this by adding an inbound rule to the security group that allows SSH traffic (TCP port 22) from the security group of the bastion host. You can use the following command to get the security group ID of the bastion host:
```
aws ec2 describe-instances --instance-ids <instance-id> --query "Reservations[0].Instances[0].SecurityGroups[0].GroupId" --output text
``` 
Replace `<instance-id>` with the ID of the bastion host instance.

5.  Connect to the bastion host using SSH. You can do this by using the following command:
```
ssh -i <path-to-key-pair> ec2-user@<bastion-host-public-ip>
```
  Replace `<path-to-key-pair>` with the path to the private key of the key pair you specified when launching the bastion host, and `<bastion-host-public-ip>` with the public IP address of the bastion host.

Once you're connected to the bastion host, you can connect to your other instances within the same VPC using SSH, by specifying the private IP address of the instance you want to connect to. The SSH traffic will be routed through the bastion host.
