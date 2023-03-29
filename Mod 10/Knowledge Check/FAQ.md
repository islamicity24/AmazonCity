## What are some reason to use automation to provision resources?
There are several reasons to use automation to provision resources:

**Speed and efficiency**: Automation can help speed up the process of provisioning resources by reducing the time it takes to manually provision and configure infrastructure components. This can help improve efficiency and reduce the time-to-market for applications.

**Consistency and repeatability**: Automation ensures that resources are provisioned consistently and to the same specifications every time. This helps to avoid configuration drift and ensures that applications and services are deployed with the same settings across different environments.

**Cost savings**: Automation can help reduce costs by ensuring that resources are only provisioned when needed, and that they are automatically shut down when they are no longer required. This can help avoid unnecessary costs associated with idle resources.

**Reduced risk of human error**: Automation can help reduce the risk of human error, which can lead to misconfigurations and security vulnerabilities. By automating the provisioning process, you can ensure that resources are provisioned and configured correctly every time.

**Scalability**: Automation can help you scale resources up or down based on demand, ensuring that you have the resources you need to handle increased traffic or workload.

**Compliance**: Automation can help ensure that resources are provisioned in compliance with regulatory and security requirements, reducing the risk of non-compliance.

Overall, using automation to provision resources can help **improve efficiency**, **reduce costs**, and **increase the reliability** and **security of your applications and services**.

## What are some reason to use automation to provision resources? (SELECT TWO)
1. Lack of version control with manual process
2. Automation requirement for creating some resources
3. Alignment with the reliability design principle
4. Greater expence with manual processes
5. Automation requirement for high availability

2. Automation requirement for creating some resources
5. Automation requirement for high availability
The reasons to use automation to provision resources are:

**Automation requirement for creating some resources**: Some resources, such as virtual machines, containers, or databases, may require a high degree of standardization and repeatability. Automation allows creating these resources consistently and quickly, reducing errors and improving productivity.

**Automation requirement for high availability**: Automating the provisioning of resources can improve the availability of the applications running on these resources. Automation can help create redundant instances, monitor their health, and automatically replace failed instances with new ones, reducing downtime and improving the reliability of the system.

## What does AWS OpsWorks do? (Select Thrree)
1. Provides managed Chef instances
2. Automates operational tasks across AWS resources
3. Provides managed Kubernetes clusters
4. Automates server configurtion, deployment and management
5. Provides managed Puppet instances
6. Automates containersrized application deployment at scale

The correct answers are:

1. Provides managed Chef instances
4. Automates server configuration, deployment, and management
5. Provides managed Puppet instances

AWS OpsWorks is an application management service that helps automate operational tasks across AWS resources. It provides managed instances of popular configuration management tools such as Chef and Puppet, allowing users to automate server configuration, deployment, and management. OpsWorks also allows for customization of workflows, and offers features such as application monitoring and auto-scaling. However, AWS OpsWorks does not provide managed Kubernetes clusters or automate containerized application deployment at scale.

## A Fleet of Amazon Elastic Compute Cloud (Amazon EC2) instances require application patches on a regular schedule. Which approach is the most efficient? 
1. Create a scheduled task on each instance to download patches 
2. Use an Amazon CloudWatch to regularly relaunch instances by using a patched Amazon Machine Image (AMI) 
3. Update the AWS CloudFormation Stack 
4. Create a maintenance windows in AWS System Manager
The most efficient approach for patching a fleet of Amazon EC2 instances is to create a maintenance window in AWS Systems Manager  ==> 4

With a maintenance window, you can define a specific period of time during which updates, patches, and other maintenance tasks can be performed on a group of instances. You can schedule the maintenance window for a convenient time, such as during off-peak hours, and perform the updates and patches automatically without the need for manual intervention.

Using a maintenance window provides several benefits:

**Automation**: Maintenance windows enable you **to automate the patching process**, saving time and reducing the risk of human error.

**Centralized management**: With maintenance windows, you can manage the patching process for multiple instances from a central location.

**Control**: You can specify the maintenance window duration, schedule, and maintenance tasks, giving you greater control over the patching process.

**Flexibility**: You can use maintenance windows to patch different types of instances, including those running in different regions, across different accounts, and with different patching needs.

Overall, creating a maintenance window in AWS Systems Manager is the most efficient approach for patching a fleet of Amazon EC2 instances.
