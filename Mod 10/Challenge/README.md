Challenge :

# How to create and successfully validate an EC2 instance through CloudFormation using YAML in Cloud9

Asked 1 year, 7 months ago
Modified 1 year, 7 months ago
Viewed 957 times
 Part of AWS Collective

1


I am currently doing the Cloud Architecting course from Amazon. I am stuck on **Module 10 Challenge Lab - Automating Infrastructure Deployment.**

The challenges are:

Create a static website for the cafe by using AWS CloudFormation.
Store templates in a version control system.
Use a continuous delivery service, create the network and application layers for the cafe.
Define an EC2 instance resource and creating the application stack.
I am up to challenge 4 and not sure how to create a new EC2 with the following resource:

Set the Logical ID to CafeInstance (see https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resources-section-structure.html for reference, if needed)
Include an ImageId that references the LatestAmiId parameter
For InstanceType, reference the instance type parameter that you defined in the previous step.
For KeyName, use the following line of code, which references the RegionMap mapping that is already defined in the template: KeyName: !FindInMap [RegionMap, !Ref "AWS::Region", keypair]
For the InstanceProfile (the AWS Identity and Access Management, or IAM, role that is attached to the instance), specify CafeRole Note: The CafeRole IAM role already exists in your account. Attaching it grants your EC2 instance the permissions to retrieve Parameter Store values from AWS Systems Manager.
In the Properties section, include the following lines of code:

    NetworkInterfaces:

    - DeviceIndex: '0'

            AssociatePublicIpAddress: 'true'

            SubnetId: !ImportValue

              'Fn::Sub': '${CafeNetworkParameter}-SubnetID'

            GroupSet:

              - !Ref CafeSG
Analysis: The previous lines help ensure that your instance deploys to the Public Subnet that you created when you ran the café network stack. Recall that at the beginning of this task, you updated the network stack to define outputs with export names. In the preceding code, you import the value for the SubnetId. The preceding code also helps ensure that the instance you create will be in the CafeSG security group that is already defined for you in this template.

Set a tag with a key of Name and a value of Cafe Web Server Tip: Observe how a Name tag was applied to the security group resource that is already defined in the template.

In the Properties section, include the following additional UserData code:
```
    UserData:

            Fn::Base64:

              !Sub |

                #!/bin/bash

                yum -y update

                yum install -y httpd mariadb-server wget

                amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2

                systemctl enable httpd

                systemctl start httpd

                systemctl enable mariadb

                systemctl start mariadb

                wget https://aws-tc-largeobjects.s3-us-west-2.amazonaws.com/ILT-TF-200-ACACAD-20-EN/mod10-challenge/cafe-app.sh

                chmod +x cafe-app.sh

                ./cafe-app.sh
 
 ```
                
Analysis: The previous code runs on the instance at the end of the boot process. It installs an Apache HTTP web server, a MariaDB database, and PHP on the Amazon Linux instance. Next, it starts the web server and the database. Then, it downloads a script named cafe-app.sh and runs it. The cafe-app script configures the database and installs the PHP code that makes the café website function.

After you are satisfied with your template updates, save the changes. To validate the template format in the Bash terminal, run the following command:
```
    aws cloudformation validate-template --template-body file:///home/ec2-user/environment/CFTemplatesRepo/templates/cafe-app.yaml
```    
If you receive a JSON-formatted response that includes the three parameters that were defined at the top of your template, then your template passed the validation. However, if you received a ValidationError response (or some other error response), you must correct the issue. Then, save the changes and run the validate-template command again.

If your template passed the validation check, add the file to "CodeCommit". In the Bash terminal, run git commands to add the file, commit it, and push it to the repository.

I have tried to do it, but keep getting an error "An error occurred (ValidationError) when calling the ValidateTemplate operation: Template format error: YAML not well-formed. (line 52, column 9)". Here is what I have written so far for cafe-app.yaml:

```
AWSTemplateFormatVersion: 2010-09-09
Description: Cafe application

Parameters:
  InstanceTypeParameter:
    Type: String
    Default: t2.small
    AllowedValues:
      - t2.micro
      - t2.small
      - t3.micro
      - t3.small
    Description: Enter t2.micro, t2.small, t3.micro, or t3.small. Default is t2.small.
    
  LatestAmiId:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: '/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2'

  CafeNetworkParameter:
    Type: String
    Default: update-cafe-network

Mappings:

  RegionMap:
    us-east-1:
      "keypair": "vockey"
    us-west-2:
      "keypair": "cafe-oregon"

Resources:
  CafeSG:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH, HTTP access
      VpcId: !ImportValue
        'Fn::Sub': '${CafeNetworkParameter}-VpcID'
      Tags:
        - Key: Name
          Value: CafeSG
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '80'
          ToPort: '80'
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: 0.0.0.0/0

  Logical ID: CafeInstance
    Type: 'AWS::E2::Instance'
    Properties:
      ImageId: !Ref LatestAmiId
      InstanceType: !Ref InstanceTypeParameter
      KeyName: !FindInMap [RegionMap, !Ref "AWS::Region", keypair]
      IamInstanceProfile: CafeRole
      NetworkInterfaces:
       - DeviceIndex: '0'
      AssociatePublicIpAddress:
      'true'
        SubnetId: !ImportValue
          'Fn::Sub': '${CafeNetworkParameter}-SubnetID'
        GroupSet:
          - !Ref CafeSG
      
      Tags:
        - Key: Name
        - Value: Cafe Web Server
        
      UserData:
        Fn::Base64:
          !Sub |
          
            #!/bin/bash

            yum -y update

            yum install -y httpd mariadb-server wget

            amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2

            systemctl enable httpd

            systemctl start httpd

            systemctl enable mariadb

            systemctl start mariadb

            wget https://aws-tc-largeobjects.s3-us-west-2.amazonaws.com/ILT-TF-200-ACACAD-20-EN/mod10-challenge/cafe-app.sh

            chmod +x cafe-app.sh

            ./cafe-app.sh
  
Outputs:
  WebServerPublicIP:
    Value: !GetAtt 'CafeInstance.PublicIp'
```
I really appreciate all the help that I can get. Thank you :)

amazon-web-servicesyamlaws-cloudformationaws-cloud9
Share
Follow
edited Aug 22, 2021 at 6:09
asked Aug 21, 2021 at 7:57
Dan's user avatar
Dan
1122 bronze badges
From AWS Academy? – 
Marcin
 Aug 21, 2021 at 8:07
ALso you need to provide your template with EC2 instance. So far you only provided networking template which is fine. – 
Marcin
 Aug 21, 2021 at 8:12
Yes, it is from AWS Academy. – 
Dan
 Aug 21, 2021 at 15:23
I just realise I posted the wrong template. My networking template is alright, I am having problems with my application template. I will edit the template tomorrow morning. – 
Dan
 Aug 21, 2021 at 15:31
Add a comment
1 Answer
Sorted by:

Highest score (default)

2


Your template **has many issues**, such as

- incorrect syntax
- missing instance profile


I fixed them, and it deploys now. I don't know if UserData works or not, but at least the syntax is correct now:
```
AWSTemplateFormatVersion: 2010-09-09
Description: Cafe application

Parameters:
  InstanceTypeParameter:
    Type: String
    Default: t2.small
    AllowedValues:
      - t2.micro
      - t2.small
      - t3.micro
      - t3.small
    Description: Enter t2.micro, t2.small, t3.micro, or t3.small. Default is t2.small.
    
  LatestAmiId:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: '/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2'

  CafeNetworkParameter:
    Type: String
    Default: update-cafe-network

Mappings:

  RegionMap:
    us-east-1:
      "keypair": "vockey"
    us-west-2:
      "keypair": "cafe-oregon"

Resources:
  CafeSG:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH, HTTP access
      VpcId: !ImportValue
        'Fn::Sub': '${CafeNetworkParameter}-VpcID'
      Tags:
        - Key: Name
          Value: CafeSG
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '80'
          ToPort: '80'
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: 0.0.0.0/0

  CafeInstance:
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: !Ref LatestAmiId
      InstanceType: !Ref InstanceTypeParameter
      KeyName: !FindInMap [RegionMap, !Ref "AWS::Region", keypair]
      #IamInstanceProfile: CafeRole
      NetworkInterfaces:
       - DeviceIndex: '0'
         AssociatePublicIpAddress: true
         SubnetId: !ImportValue
          'Fn::Sub': '${CafeNetworkParameter}-SubnetID'
         GroupSet:
           - !Ref CafeSG      
      Tags:
        - Key: Name
          Value: Cafe Web Server
        
      UserData:
        Fn::Base64:
          !Sub |          
            #!/bin/bash

            yum -y update

            yum install -y httpd mariadb-server wget

            amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2

            systemctl enable httpd

            systemctl start httpd

            systemctl enable mariadb

            systemctl start mariadb

            wget https://aws-tc-largeobjects.s3-us-west-2.amazonaws.com/ILT-TF-200-ACACAD-20-EN/mod10-challenge/cafe-app.sh

            chmod +x cafe-app.sh

            ./cafe-app.sh
  
Outputs:
  WebServerPublicIP:
    Value: !GetAtt 'CafeInstance.PublicIp'
```


answered Aug 23, 2021 at 6:25
