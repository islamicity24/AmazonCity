![image](https://user-images.githubusercontent.com/126258837/230409955-318cb37e-8231-45d3-85dd-43205b410ef5.png)

The two attributes that are reasons to choose Amazon Elastic Compute Cloud (Amazon EC2) are:

A. Ability to run any type of workload: Amazon EC2 provides a flexible and scalable infrastructure that allows you to run any type of workload, including web applications, batch processing, and big data analysis.

E. Complete control of computing resources: Amazon EC2 gives you complete control over your computing resources, including the ability to choose the type of instance you want, the operating system you want to use, and the software that you want to install. This allows you to optimize your infrastructure to meet your specific requirements and to manage your costs more effectively.

![image](https://user-images.githubusercontent.com/126258837/230414580-38500d12-73bc-48c7-bb53-0d120e90a4ec.png)


## 2. What are the benefits of using an Amazon Machine Image (AMI)?
(Select THREE.)

A. Migrating data from on-premises to Amazon EC2 instances

B. Launching instances with the same configuration

C. Using an AMI as a server backup for Amazon Elastic Compute Cloud
(Amazon EC2) instances

D. Automating security group settings for instances

E. Selling or sharing software solutions packaged as an AMI

The three benefits of using an Amazon Machine Image (AMI) are:

B. Launching instances with the same configuration: An AMI is a pre-configured virtual machine image that contains the necessary information to launch an instance with the desired operating system, applications, and data. This makes it easy to launch instances with the same configuration and software stack, reducing the time and effort required for deployment and configuration.

C. Using an AMI as a server backup for Amazon Elastic Compute Cloud (Amazon EC2) instances: An AMI can be used as a backup of an EC2 instance, providing a quick and easy way to recover from a disaster or to launch a new instance with the same configuration as the original.

E. Selling or sharing software solutions packaged as an AMI: An AMI can be used to package software solutions, making it easy to distribute and share them with others. This allows software vendors to sell and distribute their software solutions through the AWS Marketplace or to share them with customers and partners.

![image](https://user-images.githubusercontent.com/126258837/230417455-ea87099c-a982-423e-8a27-761c9ed9fca8.png)


## 3. A system administrator must change the instance types of multiple running Amazon
Elastic Compute Cloud (Amazon EC2) instances. The instances were launched with a
mix of Amazon Elastic Block Store (Amazon EBS)-backed Amazon Machine Images
(AMIs) and instance store-backed AMIs. Which method is a valid way to change the
instance type?

A. Change the instance type of an Amazon EBS-backed instance without
stopping it.

B. Change the instance type of an instance store-backed instance without
stopping it.

C. Stop an Amazon EBS-backed instance , change its instance type, and start
the instance.

D. Stop an instance store-backed instance, change its instance type, and start
the instance.
The valid way to change the instance type of multiple running Amazon Elastic Compute Cloud (Amazon EC2) instances that were launched with a mix of Amazon Elastic Block Store (Amazon EBS)-backed Amazon Machine Images (AMIs) and instance store-backed AMIs is:

C. Stop an Amazon EBS-backed instance, change its instance type, and start the instance.

Changing the instance type of an Amazon EBS-backed instance without stopping it is not possible. You must stop the instance first, change its instance type, and then start it again.

Changing the instance type of an instance store-backed instance without stopping it is also not possible. You must stop the instance first, change its instance type, and then start it again.

However, changing the instance type of an Amazon EBS-backed instance after stopping it is possible. When you stop an Amazon EBS-backed instance, its root EBS volume is preserved, so you can change its instance type and then start it again. The process involves stopping the instance, modifying the instance type of the instance, and then starting the instance.

![image](https://user-images.githubusercontent.com/126258837/230419496-595cb697-0679-4c13-976f-fc23a793332c.png)


## 4. A workload reguires high read/write access to large local datasets. Which instance
types would perform best for this workload? (Select TWO.)

A. General purpose
B. Compute optimized
C. Memory optimized
D. Accelerated computing
E. Storage optimized
The two instance types that would perform best for a workload that requires high read/write access to large local datasets are:

B. Compute optimized: Compute optimized instances are designed for high-performance computing and can deliver high CPU performance, which can help improve the speed of read and write operations.

E. Storage optimized: Storage optimized instances are designed for workloads that require high storage capacity and throughput. These instances are optimized for accessing large datasets and can provide high read/write performance to local storage.

General purpose instances are suitable for a wide range of workloads, but they may not provide the performance required for high read/write access to large local datasets.

Memory optimized instances are designed for workloads that require high memory capacity and are not optimized for local storage access.

Accelerated computing instances are designed for workloads that require high-performance computing using specialized hardware accelerators, such as graphics processing units (GPUs). These instances are not optimized for local storage access.

![image](https://user-images.githubusercontent.com/126258837/230422547-402826d4-64dd-4ac4-ab4c-f9ba0781e0e0.png)

B & D are correct answers

## 5. An application requires the media access control (MAC) address of the host Amazon Elastic Compute Cloud (Amazon EC2) instance. The architecture uses an AWS Auto Scaling group to dynamically launch and terminate instances. Which way is best for the application to obtain the MAC address? 

A. Write the MAC address in the application configuration file of each instance.

B. Include the MAC address in the Amazon Machine Image (AM I) that is used to launch all of the instances in the AWS Auto Scaling group. 

C. Include the MAC address in a custom AMI for each instance in the AWS Auto Scaling group. 

D. Use the user data of each instance to access the MAC address through the instance metadata. 

The best way for the application to obtain the media access control (MAC) address of the host Amazon Elastic Compute Cloud (Amazon EC2) instance that is launched by an AWS Auto Scaling group is:

D. Use the user data of each instance to access the MAC address through the instance metadata.

The instance metadata service provides information about an Amazon EC2 instance, such as the MAC address, that can be accessed from within the instance using a simple HTTP request. The user data can be used to execute scripts or retrieve metadata during instance launch. Therefore, you can use the user data to execute a script that retrieves the MAC address from the instance metadata service and then make it available to the application.

Writing the MAC address in the application configuration file of each instance is not a scalable solution because it would require updating the configuration file each time a new instance is launched or an existing instance is terminated.

Including the MAC address in the Amazon Machine Image (AMI) or a custom AMI is not recommended because it can lead to security issues, as the MAC address is specific to the instance and should not be included in the AMI. Additionally, including the MAC address in an AMI would not be a scalable solution because it would require creating a new AMI each time a new instance is launched or an existing instance is terminated.
