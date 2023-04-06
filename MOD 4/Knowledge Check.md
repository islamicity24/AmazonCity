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

![image](https://user-images.githubusercontent.com/126258837/230425678-b35cce90-ba72-461e-9ab3-f1f9b19e3167.png)


A transactional workload on an Amazon Elastic Compute Cloud (Amazon EC2) instance performs high amounts of frequent read and write operations. Which Amazon Elastic Block Store (Amazon EBS) volume type is best for this workload?

A. General purpose solid state drive (SSD)
B. Cold hard disk drive (HDD)
C. Provisioned IOPS solid state drive (SSD)
D. Throughput optimized hard disk drive (HDD) 

For a transactional workload that performs high amounts of frequent read and write operations on an Amazon Elastic Compute Cloud (Amazon EC2) instance, the best Amazon Elastic Block Store (Amazon EBS) volume type is:

C. Provisioned IOPS solid state drive (SSD)

Provisioned IOPS SSD volumes are designed to provide predictable, high-performance storage for I/O-intensive transactional workloads, such as databases. Provisioned IOPS SSD volumes offer the highest performance and lowest latency among all Amazon EBS volume types. The IOPS and throughput can be provisioned independently, allowing you to optimize both performance and cost for your workload.

General Purpose SSD volumes and Throughput Optimized HDD volumes are better suited for mixed-workloads and workloads that require high throughput and large block sizes, respectively.

Cold HDD volumes are designed for infrequently accessed workloads that require low-cost storage.

![image](https://user-images.githubusercontent.com/126258837/230426855-f351b65d-2a5b-4fa1-9f76-0bccacd3efc7.png)


## 7. it is possible to create an NFS share on an Amazon EBS-backed Linux instance by installing and configuring an NFS server on the instance. in this way, multiple Linux systems can share the file system of that instance. Which advantages does Amazon Elastic File System (Amazon EFS) provide, compared to this solution? (Select TWO.) 

A. Strong consistency 

B. Automatic scaling 

C. High availability 

D. File locking 

E. No need for backups 

The advantages of using Amazon Elastic File System (Amazon EFS) compared to creating an NFS share on an Amazon EBS-backed Linux instance are:

B. Automatic scaling: Amazon EFS can automatically scale the file system storage capacity and throughput as the number of concurrent clients or file operations increase or decrease.

C. High availability: Amazon EFS is designed to provide high availability and durability, with data stored across multiple availability zones in a region.

A. Strong consistency: Amazon EFS provides strong read-after-write consistency for all file operations, which means that any read operation after a write operation will return the updated data.

D. File locking: Amazon EFS supports file locking, which is important in environments where multiple clients may be accessing the same file simultaneously.

E. Need for backups: While Amazon EFS does provide redundancy and durability, it is still recommended to have backups of important data, as with any storage solution.

![image](https://user-images.githubusercontent.com/126258837/230428397-b249b22c-2e4d-4d5e-9687-fee0330ea76d.png)


## 8. Which feature does Amazon FSx for Windows File Server provide? 

A. Fully managed Windows file servers 

B. Microsoft Active Directory (Microsoft AD) server for Windows file servers 

C. Backup solution for on-premises Windows file servers 

D. Amazon management agent for Windows file serve 

So .. Amazon FSx for Windows File Server provides fully managed Windows file servers.

![image](https://user-images.githubusercontent.com/126258837/230429707-d87db088-8d67-4a31-b262-2b43a2baceea.png)


Which descriptions of Amazon Elastic Compute Cloud (Amazon EC2) pricing options are correct? (Select TWO.) 

A. On-Demand instances enable you to pay for compute capacity by usage time, with no long-term commitments. 
B. Reserved Instances are physical servers that are reserved exclusively for your use. 
C. Savings Plans are budgeting tools that help you manage Amazon EC2 costs. 
D. Dedicated Hosts are servers that are dedicated to one purpose, such as a firewall. 
E. Spot Instances offer spare compute capacity at discounted prices, and can be interrupted. 
The correct descriptions of Amazon Elastic Compute Cloud (Amazon EC2) pricing options are:

A. On-Demand instances enable you to pay for compute capacity by usage time, with no long-term commitments.

E. Spot Instances offer spare compute capacity at discounted prices, and can be interrupted.

![image](https://user-images.githubusercontent.com/126258837/230430865-fb20ff97-9270-4297-ab6c-593c876bb29d.png)

A company has three high-performance computing instances that must communicate with each other. The company would like to achieve maximum network performance between the instances. The most important requirement is that these systems do not share the same rack. Which placement strategy should they use? 

A. Partition 
B. Spread 
C. Cluster 
D. Default 
The placement strategy that the company should use is "Spread."

In Spread placement groups, instances are placed on distinct underlying hardware to reduce correlated failures. Therefore, it satisfies the requirement that these systems do not share the same rack. Additionally, instances in Spread placement groups can achieve higher network throughput and lower network latency compared to instances outside of a placement group.

![image](https://user-images.githubusercontent.com/126258837/230432083-85dff28c-e668-4355-b154-d96690a1a59b.png)


# AWS adalah platform layanan cloud computing yang memungkinkan pelanggan untuk menyewa komputasi, penyimpanan, dan layanan lainnya di lingkungan cloud AWS.

Ada banyak layanan yang disediakan oleh AWS, seperti Amazon Elastic Compute Cloud (EC2), Amazon Simple Storage Service (S3), Amazon RDS, dan masih banyak lagi. Amazon EC2 adalah layanan komputasi awan yang menyediakan kapasitas komputasi skala web dengan menggunakan instance virtual yang disebut EC2 instances. Amazon S3 adalah layanan penyimpanan awan untuk menyimpan dan mengambil data dari internet.

AWS memiliki banyak keuntungan, seperti:

Skala elastis: AWS memungkinkan Anda untuk menambah atau mengurangi kapasitas dengan cepat dan mudah. Anda dapat memulai dengan sumber daya kecil dan menambahkannya saat permintaan meningkat.

Biaya efektif: AWS memungkinkan Anda membayar hanya untuk sumber daya yang Anda gunakan. Anda tidak perlu mengeluarkan biaya awal yang besar atau mengeluarkan biaya operasional yang tinggi.

Keamanan: AWS menyediakan berbagai fitur keamanan untuk melindungi data Anda, seperti firewalls, enkripsi, dan kontrol akses.

Fleksibilitas: AWS menawarkan banyak layanan yang dapat disesuaikan dengan kebutuhan bisnis Anda. Anda dapat memilih layanan dan konfigurasinya sesuai dengan kebutuhan Anda.

Itu hanya beberapa keuntungan dari AWS, tetapi ada banyak lagi. AWS adalah platform yang sangat berguna bagi perusahaan yang ingin meningkatkan efisiensi operasional mereka dan mengurangi biaya infrastruktur teknologi mereka.

