# Module 4 - Guided Lab: Introducing Amazon Elastic File System (Amazon EFS)
Lab overview and objectives
This lab introduces you to Amazon Elastic File System (Amazon EFS) by using the AWS Management Console.

After completing this lab, you should be able to:

- Log in to the AWS Management Console
- Create an Amazon EFS file system
Log in to an Amazon Elastic Compute Cloud (Amazon EC2) instance that runs Amazon Linux
Mount your file system to your EC2 instance
Examine and monitor the performance of your file system

Duration
This lab requires approximately 20 minutes to complete.


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


## Task 1: Creating a security group to access your EFS file system
The security group that you associate with a mount target must allow inbound access for TCP on port 2049 for Network File System (NFS). This is the security group that you will now create, configure, and attach to your EFS mount targets.

In the AWS Management Console, on the Services menu, choose EC2.

In the navigation pane on the left, choose Security Groups.

Copy the Security group ID of the EFSClient security group to your text editor.

The Group ID should look similar to sg-03727965651b6659b.

8. Choose **Create security group** then configure:


Security group name: EFS Mount Target
Description: Inbound NFS access from EFS clients
VPC: Lab VPC
Under the Inbound rules section, choose Add rule then configure:

Type: NFS

Source:

Custom
In the Custom box, paste the security group's Security group ID that you copied to your text editor
Choose Create security group.


Task 2: Creating an EFS file system
EFS file systems can be mounted to multiple EC2 instances that run in different Availability Zones in the same Region. These instances use mount targets that are created in each Availability Zone to mount the file system by using standard NFSv4.1 semantics. You can mount the file system on instances in only one virtual private cloud (VPC) at a time. Both the file system and the VPC must be in the same Region.

On the Services menu, choose EFS.

Choose Create file system

In the Create file system window, choose Customize

On Step 1:

Uncheck  Enable automatic backups.

Lifecycle management: Select None

In the Tags section, configure:

Key: Name
Value: My First EFS File System
Choose Next

For VPC, select Lab VPC.

Detach the default security group from each Availability Zone mount target by choosing the  check box on each default security group.

Attach the EFS Mount Target security group to each Availability Zone mount target by:

Selecting each Security groups check box.

Choosing EFS Mount Target.

A mount target is created for each subnet.

Your mount targets should look like the following example. The diagram shows two mount targets in the Lab VPC that use the EFS Mount Target security group. In this lab, you should be using the Lab VPC.

Target Security Groups

Choose Next
On Step 3, choose Next
On Step 4:
Review your configuration.
Choose Create
 Congratulations! You have created a new EFS file system in your Lab VPC and mount targets in each Lab VPC subnet. In a few seconds, the File system state of the file system will change to Available, followed by the mount targets 2–3 minutes later.

Proceed to the next step after the Mount target state for each mount target changes to Available. Choose the screen refresh button after 2–3 minutes to check its progress.

Note: You may need to scroll to the right in the File systems pane to find the File system state.


Task 3: Connecting to your EC2 instance via SSH
In this task, you will connect to your EC2 instance by using Secure Shell (SSH).

 Microsoft Windows users
 These instructions are specifically for Microsoft Windows users. If you are using macOS or Linux, skip to the next section.
​

Above these instructions that you are currently reading, choose the Details dropdown menu, and then select Show
   A Credentials window opens.

Choose the Download PPK button and save the labsuser.ppk file.
   Note: Typically, your browser saves the file to the Downloads directory.

Note the EC2PublicIP address, if it is displayed.
Exit the Details panel by choosing the X.
To use SSH to access the EC2 instance, you must use *PuTTY*. If you do not have PuTTY installed on your computer, download PuTTY.
Open putty.exe.
To keep the PuTTY session open for a longer period of time, configure the PuTTY timeout:
Choose Connection
Seconds between keepalives: 30
Configure your PuTTY session by using the following settings.
Choose Session

Host Name (or IP address): Paste the EC2PublicIP for the instance you noted earlier

Alternatively, return to the Amazon EC2 console and choose Instances
Select the instance you want to connect to
In the Description tab, copy the IPv4 Public IP value
Back in PuTTY, in the Connection list, expand  SSH

Choose Auth and expand  Credentials

Under Private key file for authentication: Choose Browse

Browse to the labsuser.ppk file that you downloaded, select it, and choose Open

Choose Open again

To trust and connect to the host, choose Accept.

When you are prompted with login as, enter: ec2-user

This action connects you to the EC2 instance.

Microsoft Windows users: Choose this link to skip ahead to the next task.

 


macOS  and Linux  users
These instructions are specifically for macOS or Linux users. If you are a Windows user, skip ahead to the next task.

Above these instructions that you are currently reading, choose the Details dropdown menu, and then select Show

A Credentials window opens.

Choose the Download PEM button and save the labsuser.pem file.

Note the EC2PublicIP address, if it is displayed.

Exit the Details panel by choosing the X.

Open a terminal window, and change directory to the directory where the labsuser.pem file was downloaded by using the cd command.

For example, if the labsuser.pem file was saved to your Downloads directory, run this command:

cd ~/Downloads
Change the permissions on the key to be read-only, by running this command:

chmod 400 labsuser.pem
Run the following command (replace <public-ip> with the EC2PublicIP address that you copied earlier).

Alternatively, to find the IP address of the on-premises instance, return to the Amazon EC2 console and select Instances
Select the instance that you want to connect to
In the Description tab, copy the IPv4 Public IP value
ssh -i labsuser.pem ec2-user@<public-ip>
When you are prompted to allow the first connection to this remote SSH server, enter yes.

Because you are using a key pair for authentication, you are not prompted for a password.



Task 4: Creating a new directory and mounting the EFS file system
 Amazon EFS supports the NFSv4.1 and NFSv4.0 protocols when it mounts your file systems on EC2 instances. Though NFSv4.0 is supported, we recommend that you use NFSv4.1. When you mount your EFS file system on your EC2 instance, you must also use an NFS client that supports your chosen NFSv4 protocol. The EC2 instance that was launched as a part of this lab includes an NFSv4.1 client, which is already installed on it.

In your SSH session, make a new directory by entering sudo mkdir efs

Back in the AWS Management Console, on the Services menu, choose EFS.

Choose My First EFS File System.

In the Amazon EFS Console, on the top right corner of the page, choose Attach to open the Amazon EC2 mount instructions.

Copy the entire command in the Using the NFS client section.

The mount command should look similar to this example:

sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-bce57914.efs.us-west-2.amazonaws.com:/ efs

 The provided sudo mount... command uses the default Linux mount options.

In your Linux SSH session, mount your Amazon EFS file system by:

Pasting the command
Pressing ENTER
Get a full summary of the available and used disk space usage by entering:

sudo df -hT

This following screenshot is an example of the output from the following disk filesystem command: 

df -hT

Notice the Type and Size of your mounted EFS file system.

disk space


Task 5: Examining the performance behavior of your new EFS file system
Examining the performance by using Flexible IO
 Flexible IO (fio) is a synthetic I/O benchmarking utility for Linux. It is used to benchmark and test Linux I/O subsystems. During boot, fio was automatically installed on your EC2 instance.

Examine the write performance characteristics of your file system by entering:

sudo fio --name=fio-efs --filesize=10G --filename=./efs/fio-efs-test.img --bs=1M --nrfiles=1 --direct=1 --sync=0 --rw=write --iodepth=200 --ioengine=libaio
 The fio command will take 5–10 minutes to complete. The output should look like the example in the following screenshot. Make sure that you examine the output of your fio command, specifically the summary status information for this WRITE test.

fio


Monitoring performance by using Amazon CloudWatch
In the AWS Management Console, on the Services menu, choose CloudWatch.

In the navigation pane on the left, choose Metrics.

In the All metrics tab, choose EFS.

Choose File System Metrics.

Select the row that has the PermittedThroughput Metric Name.

 You might need to wait 2–3 minutes and refresh the screen several times before all available metrics, including PermittedThroughput, calculate and populate.

On the graph, choose and drag around the data line. If you do not see the line graph, adjust the time range of the graph to display the period during which you ran the fio command.

choose drag

Pause your pointer on the data line in the graph. The value should be 105M.

Throughput

The throughput of Amazon EFS scales as the file system grows. File-based workloads are typically spiky. They drive high levels of throughput for short periods of time, and low levels of throughput the rest of the time. Because of this behavior, Amazon EFS is designed to burst to high throughput levels for periods of time. All file systems, regardless of size, can burst to 100 MiB/s of throughput. For more information about performance characteristics of your EFS file system, see the official Amazon Elastic File System documentation.

In the All metrics tab, uncheck the box for PermittedThroughput.

Select the check box for DataWriteIOBytes.

 If you do not see DataWriteIOBytes in the list of metrics, use the File System Metrics search to find it.

Choose the Graphed metrics tab.

On the Statistics column, select Sum.

On the Period column, select 1 Minute.

Pause your pointer on the peak of the line graph. Take this number (in bytes) and divide it by the duration in seconds (60 seconds). The result gives you the write throughput (B/s) of your file system during your test.

Sum 1 Minute

The throughput that is available to a file system scales as a file system grows. All file systems deliver a consistent baseline performance of 50 MiB/s per TiB of storage. Also, all file systems (regardless of size) can burst to 100 MiB/s. File systems that are larger than 1T B can burst to 100 MiB/s per TiB of storage. As you add data to your file system, the maximum throughput that is available to the file system scales linearly and automatically with your storage.

File system throughput is shared across all EC2 instances that are connected to a file system. For more information about performance characteristics of your EFS file system, see the official Amazon Elastic File System documentation.

 Congratulations! You created an EFS file system, mounted it to an EC2 instance, and ran an I/O benchmark test to examine its performance characteristics.


Submitting your work
At the top of these instructions, choose Submit to record your progress and when prompted, choose Yes.

If the results don't display after a couple of minutes, return to the top of these instructions and choose Grades

Tip: You can submit your work multiple times. After you change your work, choose Submit again. Your last submission is what will be recorded for this lab.

To find detailed feedback on your work, choose Details followed by  View Submission Report.


Lab complete 
 Congratulations! You have completed the lab.

Choose End Lab at the top of this page, and then select Yes to confirm that you want to end the lab.

A panel should appear with this message: DELETE has been initiated... You may close this message box now.​

Select the X in the top right corner to close the panel.

 
 
 
 
 
 
 
 
 [ec2-user@ip-10-0-1-123 ~]$ sudo mkdir EFS
[ec2-user@ip-10-0-1-123 ~]$ sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-0d4a1bffc22283e04.efs.us-east-1.amazonaws.com:/ efs
mount.nfs4: mount point efs does not exist
[ec2-user@ip-10-0-1-123 ~]$ cd EFS
[ec2-user@ip-10-0-1-123 EFS]$ sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-0d4a1bffc22283e04.efs.us-east-1.amazonaws.com:/ efs
mount.nfs4: mount point efs does not exist
[ec2-user@ip-10-0-1-123 EFS]$ ls
[ec2-user@ip-10-0-1-123 EFS]$ cd efs
-bash: cd: efs: No such file or directory
[ec2-user@ip-10-0-1-123 EFS]$ cd ..
[ec2-user@ip-10-0-1-123 ~]$ sudo mkdir efs
[ec2-user@ip-10-0-1-123 ~]$ ls
efs  EFS
[ec2-user@ip-10-0-1-123 ~]$ sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-0d4a1bffc22283e04.efs.us-east-1.amazonaws.com:/ efs
[ec2-user@ip-10-0-1-123 ~]$ sudo df -hT
Filesystem                                         Type      Size  Used Avail Use% Mounted on
devtmpfs                                           devtmpfs  478M     0  478M   0% /dev
tmpfs                                              tmpfs     486M     0  486M   0% /dev/shm
tmpfs                                              tmpfs     486M  412K  485M   1% /run
tmpfs                                              tmpfs     486M     0  486M   0% /sys/fs/cgroup
/dev/xvda1                                         xfs       8.0G  1.9G  6.2G  23% /
tmpfs                                              tmpfs      98M     0   98M   0% /run/user/1000
fs-0d4a1bffc22283e04.efs.us-east-1.amazonaws.com:/ nfs4      8.0E     0  8.0E   0% /home/ec2-user/efs
[ec2-user@ip-10-0-1-123 ~]$ sudo fio --name=fio-efs --filesize=10G --filename=./efs/fio-efs-test.img --bs=1M --nrfiles=1 --direct=1 --sync=0 --rw=write --iodepth=200 --ioengine=libaio
fio-efs: (g=0): rw=write, bs=1M-1M/1M-1M/1M-1M, ioengine=libaio, iodepth=200
fio-2.14
Starting 1 process
fio-efs: Laying out IO file(s) (1 file(s) / 10240MB)
Jobs: 1 (f=1): [W(1)] [2.5% done] [0KB/98402KB/0KB /s] [0/96/0 iops] [eta 01m:58Jobs: 1 (f=1): [W(1)] [3.4% done] [0KB/98402KB/0KB /s] [0/96/0 iops] [eta 01m:53Jobs: 1 (f=1): [W(1)] [4.3% done] [0KB/98402KB/0KB /s] [0/96/0 iops] [eta 01m:50Jobs: 1 (f=1): [W(1)] [5.3% done] [0KB/92252KB/0KB /s] [0/90/0 iops] [eta 01m:48Jobs: 1 (f=1): [W(1)] [6.2% done] [0KB/99.11MB/0KB /s] [0/99/0 iops] [eta 01m:46Jobs: 1 (f=1): [W(1)] [7.1% done] [0KB/97377KB/0KB /s] [0/95/0 iops] [eta 01m:44Jobs: 1 (f=1): [W(1)] [8.0% done] [0KB/95327KB/0KB /s] [0/93/0 iops] [eta 01m:43Jobs: 1 (f=1): [W(1)] [9.0% done] [0KB/97377KB/0KB /s] [0/95/0 iops] [eta 01m:41Jobs: 1 (f=1): [W(1)] [9.9% done] [0KB/94302KB/0KB /s] [0/92/0 iops] [eta 01m:40Jobs: 1 (f=1): [W(1)] [10.8% done] [0KB/95327KB/0KB /s] [0/93/0 iops] [eta 01m:3Jobs: 1 (f=1): [W(1)] [11.7% done] [0KB/95327KB/0KB /s] [0/93/0 iops] [eta 01m:3Jobs: 1 (f=1): [W(1)] [12.6% done] [0KB/94302KB/0KB /s] [0/92/0 iops] [eta 01m:3Jobs: 1 (f=1): [W(1)] [13.5% done] [0KB/95327KB/0KB /s] [0/93/0 iops] [eta 01m:3Jobs: 1 (f=1): [W(1)] [14.4% done] [0KB/92252KB/0KB /s] [0/90/0 iops] [eta 01m:3Jobs: 1 (f=1): [W(1)] [15.3% done] [0KB/94302KB/0KB /s] [0/92/0 iops] [eta 01m:3Jobs: 1 (f=1): [W(1)] [16.2% done] [0KB/93277KB/0KB /s] [0/91/0 iops] [eta 01m:3Jobs: 1 (f=1): [W(1)] [17.1% done] [0KB/91227KB/0KB /s] [0/89/0 iops] [eta 01m:3Jobs: 1 (f=1): [W(1)] [18.0% done] [0KB/93277KB/0KB /s] [0/91/0 iops] [eta 01m:3Jobs: 1 (f=1): [W(1)] [18.9% done] [0KB/92252KB/0KB /s] [0/90/0 iops] [eta 01m:3Jobs: 1 (f=1): [W(1)] [19.6% done] [0KB/90202KB/0KB /s] [0/88/0 iops] [eta 01m:3Jobs: 1 (f=1): [W(1)] [20.5% done] [0KB/91227KB/0KB /s] [0/89/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [21.4% done] [0KB/91227KB/0KB /s] [0/89/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [22.3% done] [0KB/90202KB/0KB /s] [0/88/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [23.2% done] [0KB/90202KB/0KB /s] [0/88/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [24.1% done] [0KB/88152KB/0KB /s] [0/86/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [24.8% done] [0KB/88152KB/0KB /s] [0/86/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [25.7% done] [0KB/90202KB/0KB /s] [0/88/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [26.5% done] [0KB/87127KB/0KB /s] [0/85/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [27.4% done] [0KB/87040KB/0KB /s] [0/85/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [28.3% done] [0KB/84052KB/0KB /s] [0/82/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [28.9% done] [0KB/88152KB/0KB /s] [0/86/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [29.8% done] [0KB/86102KB/0KB /s] [0/84/0 iops] [eta 01m:2Jobs: 1 (f=1): [W(1)] [30.7% done] [0KB/86102KB/0KB /s] [0/84/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [31.6% done] [0KB/84992KB/0KB /s] [0/83/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [32.5% done] [0KB/85077KB/0KB /s] [0/83/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [33.0% done] [0KB/84052KB/0KB /s] [0/82/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [33.9% done] [0KB/84052KB/0KB /s] [0/82/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [34.8% done] [0KB/82002KB/0KB /s] [0/80/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [35.7% done] [0KB/81920KB/0KB /s] [0/80/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [36.2% done] [0KB/82002KB/0KB /s] [0/80/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [37.1% done] [0KB/82002KB/0KB /s] [0/80/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [37.6% done] [0KB/63551KB/0KB /s] [0/62/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [38.5% done] [0KB/97377KB/0KB /s] [0/95/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [39.3% done] [0KB/77901KB/0KB /s] [0/76/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [40.2% done] [0KB/80976KB/0KB /s] [0/79/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [41.0% done] [0KB/78926KB/0KB /s] [0/77/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [41.5% done] [0KB/77901KB/0KB /s] [0/76/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [42.4% done] [0KB/76876KB/0KB /s] [0/75/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [43.2% done] [0KB/77901KB/0KB /s] [0/76/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [43.7% done] [0KB/74826KB/0KB /s] [0/73/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [44.5% done] [0KB/72776KB/0KB /s] [0/71/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [45.4% done] [0KB/77901KB/0KB /s] [0/76/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [45.8% done] [0KB/73801KB/0KB /s] [0/72/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [46.7% done] [0KB/70656KB/0KB /s] [0/69/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [47.5% done] [0KB/74826KB/0KB /s] [0/73/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [47.9% done] [0KB/72776KB/0KB /s] [0/71/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [48.8% done] [0KB/71751KB/0KB /s] [0/70/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [49.6% done] [0KB/71751KB/0KB /s] [0/70/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [50.0% done] [0KB/68676KB/0KB /s] [0/67/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [50.8% done] [0KB/69701KB/0KB /s] [0/68/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [51.2% done] [0KB/67651KB/0KB /s] [0/66/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [52.0% done] [0KB/67651KB/0KB /s] [0/66/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [52.8% done] [0KB/66626KB/0KB /s] [0/65/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [53.2% done] [0KB/66626KB/0KB /s] [0/65/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [54.0% done] [0KB/65601KB/0KB /s] [0/64/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [54.4% done] [0KB/64576KB/0KB /s] [0/63/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [55.2% done] [0KB/61501KB/0KB /s] [0/60/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [55.6% done] [0KB/63551KB/0KB /s] [0/62/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [56.3% done] [0KB/61501KB/0KB /s] [0/60/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [57.1% done] [0KB/60476KB/0KB /s] [0/59/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [57.5% done] [0KB/59451KB/0KB /s] [0/58/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [58.3% done] [0KB/57401KB/0KB /s] [0/56/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [58.6% done] [0KB/56376KB/0KB /s] [0/55/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [58.9% done] [0KB/56376KB/0KB /s] [0/55/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [59.7% done] [0KB/54326KB/0KB /s] [0/53/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [60.0% done] [0KB/52276KB/0KB /s] [0/51/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [60.8% done] [0KB/51251KB/0KB /s] [0/50/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [61.1% done] [0KB/50176KB/0KB /s] [0/49/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [61.4% done] [0KB/47151KB/0KB /s] [0/46/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [62.1% done] [0KB/46126KB/0KB /s] [0/45/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [62.4% done] [0KB/44076KB/0KB /s] [0/43/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [62.7% done] [0KB/41001KB/0KB /s] [0/40/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [63.0% done] [0KB/38950KB/0KB /s] [0/38/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [63.7% done] [0KB/33825KB/0KB /s] [0/33/0 iops] [eta 00m:4Jobs: 1 (f=1): [W(1)] [64.0% done] [0KB/25625KB/0KB /s] [0/25/0 iops] [eta 00m:4Jobs: 1 (f=1): [W(1)] [63.8% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:50sJobs: 1 (f=1): [W(1)] [64.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:50sJobs: 1 (f=1): [W(1)] [63.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:51sJobs: 1 (f=1): [W(1)] [64.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:51sJobs: 1 (f=1): [W(1)] [63.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:52sJobs: 1 (f=1): [W(1)] [64.1% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:52sJobs: 1 (f=1): [W(1)] [64.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:52sJobs: 1 (f=1): [W(1)] [64.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:53sJobs: 1 (f=1): [W(1)] [64.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:53sJobs: 1 (f=1): [W(1)] [64.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:53sJobs: 1 (f=1): [W(1)] [64.5% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:54sJobs: 1 (f=1): [W(1)] [64.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:54sJobs: 1 (f=1): [W(1)] [64.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:55sJobs: 1 (f=1): [W(1)] [64.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:55sJobs: 1 (f=1): [W(1)] [65.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:55sJobs: 1 (f=1): [W(1)] [64.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:56sJobs: 1 (f=1): [W(1)] [65.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:56sJobs: 1 (f=1): [W(1)] [65.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:56sJobs: 1 (f=1): [W(1)] [65.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:57sJobs: 1 (f=1): [W(1)] [65.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:57sJobs: 1 (f=1): [W(1)] [65.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:57sJobs: 1 (f=1): [W(1)] [65.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:58sJobs: 1 (f=1): [W(1)] [65.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:58sJobs: 1 (f=1): [W(1)] [65.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:59sJobs: 1 (f=1): [W(1)] [65.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:59sJobs: 1 (f=1): [W(1)] [65.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:59sJobs: 1 (f=1): [W(1)] [65.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:59sJobs: 1 (f=1): [W(1)] [65.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:00sJobs: 1 (f=1): [W(1)] [65.9% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:00sJobs: 1 (f=1): [W(1)] [65.7% done] [0KB/6150KB/0KB /s] [0/6/0 iops] [eta 01m:01sJobs: 1 (f=1): [W(1)] [65.9% done] [0KB/2050KB/0KB /s] [0/2/0 iops] [eta 01m:01sJobs: 1 (f=1): [W(1)] [66.1% done] [0KB/14350KB/0KB /s] [0/14/0 iops] [eta 01m:0Jobs: 1 (f=1): [W(1)] [66.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:01sJobs: 1 (f=1): [W(1)] [66.1% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:02sJobs: 1 (f=1): [W(1)] [66.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:02sJobs: 1 (f=1): [W(1)] [66.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:02sJobs: 1 (f=1): [W(1)] [66.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:03sJobs: 1 (f=1): [W(1)] [66.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:03sJobs: 1 (f=1): [W(1)] [66.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:03sJobs: 1 (f=1): [W(1)] [66.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:04sJobs: 1 (f=1): [W(1)] [66.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:04sJobs: 1 (f=1): [W(1)] [66.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:04sJobs: 1 (f=1): [W(1)] [67.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:04sJobs: 1 (f=1): [W(1)] [66.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:05sJobs: 1 (f=1): [W(1)] [67.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:05sJobs: 1 (f=1): [W(1)] [67.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:05sJobs: 1 (f=1): [W(1)] [67.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:06sJobs: 1 (f=1): [W(1)] [67.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:06sJobs: 1 (f=1): [W(1)] [67.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:06sJobs: 1 (f=1): [W(1)] [67.5% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:06sJobs: 1 (f=1): [W(1)] [67.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:07sJobs: 1 (f=1): [W(1)] [67.5% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:07sJobs: 1 (f=1): [W(1)] [67.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:07sJobs: 1 (f=1): [W(1)] [67.8% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:07sJobs: 1 (f=1): [W(1)] [67.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:08sJobs: 1 (f=1): [W(1)] [67.8% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:08sJobs: 1 (f=1): [W(1)] [67.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:08sJobs: 1 (f=1): [W(1)] [68.1% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:08sJobs: 1 (f=1): [W(1)] [67.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:09sJobs: 1 (f=1): [W(1)] [68.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:09sJobs: 1 (f=1): [W(1)] [68.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:09sJobs: 1 (f=1): [W(1)] [68.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:09sJobs: 1 (f=1): [W(1)] [68.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:10sJobs: 1 (f=1): [W(1)] [68.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:10sJobs: 1 (f=1): [W(1)] [68.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:10sJobs: 1 (f=1): [W(1)] [68.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:10sJobs: 1 (f=1): [W(1)] [68.8% done] [0KB/6150KB/0KB /s] [0/6/0 iops] [eta 01m:10sJobs: 1 (f=1): [W(1)] [68.6% done] [0KB/9225KB/0KB /s] [0/9/0 iops] [eta 01m:11sJobs: 1 (f=1): [W(1)] [68.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:11sJobs: 1 (f=1): [W(1)] [68.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:11sJobs: 1 (f=1): [W(1)] [69.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:11sJobs: 1 (f=1): [W(1)] [69.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:11sJobs: 1 (f=1): [W(1)] [69.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:12sJobs: 1 (f=1): [W(1)] [69.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:12sJobs: 1 (f=1): [W(1)] [69.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:12sJobs: 1 (f=1): [W(1)] [69.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:12sJobs: 1 (f=1): [W(1)] [69.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:12sJobs: 1 (f=1): [W(1)] [69.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:13sJobs: 1 (f=1): [W(1)] [69.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:13sJobs: 1 (f=1): [W(1)] [69.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:13sJobs: 1 (f=1): [W(1)] [69.7% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:13sJobs: 1 (f=1): [W(1)] [69.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:13sJobs: 1 (f=1): [W(1)] [69.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [69.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [69.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [70.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [70.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [70.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [70.1% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [70.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [70.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [70.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [70.6% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [70.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [70.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [70.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [70.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [70.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [71.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [71.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [71.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [71.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [71.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [71.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [71.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [71.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [71.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [71.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [71.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [71.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [71.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [71.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [71.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [72.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [72.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [72.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [72.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [72.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [72.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [72.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [72.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [72.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [72.7% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [72.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [72.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [72.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [73.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [73.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [73.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [73.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [73.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [73.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [73.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [73.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [73.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [73.6% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [73.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [73.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [73.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [73.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.1% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.5% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.8% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [74.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.5% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.6% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [75.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [76.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.0% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.1% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.3% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [77.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:20sJobs: 1 (f=1): [W(1)] [78.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.2% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.4% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [78.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:19sJobs: 1 (f=1): [W(1)] [79.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [79.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [79.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [79.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [79.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [79.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [79.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [79.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [79.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [79.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [79.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:18sJobs: 1 (f=1): [W(1)] [79.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [79.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [80.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [80.1% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [80.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [80.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [80.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [80.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [80.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:17sJobs: 1 (f=1): [W(1)] [80.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [80.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [80.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [80.7% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [80.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [80.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [80.9% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [80.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:16sJobs: 1 (f=1): [W(1)] [81.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [81.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [81.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [81.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [81.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [81.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [81.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [81.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:15sJobs: 1 (f=1): [W(1)] [81.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [81.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [81.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [81.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [81.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [82.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [82.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:14sJobs: 1 (f=1): [W(1)] [82.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:13sJobs: 1 (f=1): [W(1)] [82.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:13sJobs: 1 (f=1): [W(1)] [82.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:13sJobs: 1 (f=1): [W(1)] [82.4% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:13sJobs: 1 (f=1): [W(1)] [82.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:13sJobs: 1 (f=1): [W(1)] [82.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:13sJobs: 1 (f=1): [W(1)] [82.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:12sJobs: 1 (f=1): [W(1)] [82.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:12sJobs: 1 (f=1): [W(1)] [82.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:12sJobs: 1 (f=1): [W(1)] [82.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:12sJobs: 1 (f=1): [W(1)] [82.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:12sJobs: 1 (f=1): [W(1)] [82.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:12sJobs: 1 (f=1): [W(1)] [83.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:11sJobs: 1 (f=1): [W(1)] [83.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:11sJobs: 1 (f=1): [W(1)] [83.2% done] [0KB/4100KB/0KB /s] [0/4/0 iops] [eta 01m:11sJobs: 1 (f=1): [W(1)] [83.3% done] [0KB/11275KB/0KB /s] [0/11/0 iops] [eta 01m:1Jobs: 1 (f=1): [W(1)] [83.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:11sJobs: 1 (f=1): [W(1)] [83.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:11sJobs: 1 (f=1): [W(1)] [83.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:10sJobs: 1 (f=1): [W(1)] [83.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:10sJobs: 1 (f=1): [W(1)] [83.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:10sJobs: 1 (f=1): [W(1)] [83.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:10sJobs: 1 (f=1): [W(1)] [83.7% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:10sJobs: 1 (f=1): [W(1)] [84.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:09sJobs: 1 (f=1): [W(1)] [84.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:09sJobs: 1 (f=1): [W(1)] [84.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:09sJobs: 1 (f=1): [W(1)] [84.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:09sJobs: 1 (f=1): [W(1)] [84.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:09sJobs: 1 (f=1): [W(1)] [84.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:09sJobs: 1 (f=1): [W(1)] [84.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:08sJobs: 1 (f=1): [W(1)] [84.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:08sJobs: 1 (f=1): [W(1)] [84.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:08sJobs: 1 (f=1): [W(1)] [84.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:08sJobs: 1 (f=1): [W(1)] [84.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:08sJobs: 1 (f=1): [W(1)] [84.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:07sJobs: 1 (f=1): [W(1)] [84.8% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:07sJobs: 1 (f=1): [W(1)] [84.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:07sJobs: 1 (f=1): [W(1)] [84.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:07sJobs: 1 (f=1): [W(1)] [84.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:07sJobs: 1 (f=1): [W(1)] [85.1% done] [0KB/6150KB/0KB /s] [0/6/0 iops] [eta 01m:06sJobs: 1 (f=1): [W(1)] [85.1% done] [0KB/9225KB/0KB /s] [0/9/0 iops] [eta 01m:06sJobs: 1 (f=1): [W(1)] [85.2% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 01m:06sJobs: 1 (f=1): [W(1)] [85.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:06sJobs: 1 (f=1): [W(1)] [85.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:05sJobs: 1 (f=1): [W(1)] [85.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:05sJobs: 1 (f=1): [W(1)] [85.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:05sJobs: 1 (f=1): [W(1)] [85.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:05sJobs: 1 (f=1): [W(1)] [85.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:05sJobs: 1 (f=1): [W(1)] [85.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:04sJobs: 1 (f=1): [W(1)] [85.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:04sJobs: 1 (f=1): [W(1)] [85.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:04sJobs: 1 (f=1): [W(1)] [85.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:04sJobs: 1 (f=1): [W(1)] [86.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:03sJobs: 1 (f=1): [W(1)] [86.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:03sJobs: 1 (f=1): [W(1)] [86.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:03sJobs: 1 (f=1): [W(1)] [86.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:03sJobs: 1 (f=1): [W(1)] [86.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:03sJobs: 1 (f=1): [W(1)] [86.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:02sJobs: 1 (f=1): [W(1)] [86.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:02sJobs: 1 (f=1): [W(1)] [86.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:02sJobs: 1 (f=1): [W(1)] [86.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:02sJobs: 1 (f=1): [W(1)] [86.7% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 01m:01sJobs: 1 (f=1): [W(1)] [86.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:01sJobs: 1 (f=1): [W(1)] [86.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:01sJobs: 1 (f=1): [W(1)] [86.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:01sJobs: 1 (f=1): [W(1)] [87.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:00sJobs: 1 (f=1): [W(1)] [87.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:00sJobs: 1 (f=1): [W(1)] [87.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 01m:00sJobs: 1 (f=1): [W(1)] [87.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 01m:00sJobs: 1 (f=1): [W(1)] [87.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:59sJobs: 1 (f=1): [W(1)] [87.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:59sJobs: 1 (f=1): [W(1)] [87.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:59sJobs: 1 (f=1): [W(1)] [87.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:59sJobs: 1 (f=1): [W(1)] [87.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:58sJobs: 1 (f=1): [W(1)] [87.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:58sJobs: 1 (f=1): [W(1)] [87.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:58sJobs: 1 (f=1): [W(1)] [87.7% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:58sJobs: 1 (f=1): [W(1)] [87.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:57sJobs: 1 (f=1): [W(1)] [87.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:57sJobs: 1 (f=1): [W(1)] [88.0% done] [0KB/1025KB/0KB /s] [0/1/0 iops] [eta 00m:57sJobs: 1 (f=1): [W(1)] [88.0% done] [0KB/3075KB/0KB /s] [0/3/0 iops] [eta 00m:57sJobs: 1 (f=1): [W(1)] [88.0% done] [0KB/10250KB/0KB /s] [0/10/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [88.2% done] [0KB/16400KB/0KB /s] [0/16/0 iops] [eta 00m:5Jobs: 1 (f=1): [W(1)] [88.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:56sJobs: 1 (f=1): [W(1)] [88.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:56sJobs: 1 (f=1): [W(1)] [88.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:55sJobs: 1 (f=1): [W(1)] [88.5% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 00m:55sJobs: 1 (f=1): [W(1)] [88.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:55sJobs: 1 (f=1): [W(1)] [88.8% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:54sJobs: 1 (f=1): [W(1)] [88.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:54sJobs: 1 (f=1): [W(1)] [88.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:54sJobs: 1 (f=1): [W(1)] [88.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:54sJobs: 1 (f=1): [W(1)] [89.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:53sJobs: 1 (f=1): [W(1)] [89.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:53sJobs: 1 (f=1): [W(1)] [89.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:53sJobs: 1 (f=1): [W(1)] [89.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:53sJobs: 1 (f=1): [W(1)] [89.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:52sJobs: 1 (f=1): [W(1)] [89.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:52sJobs: 1 (f=1): [W(1)] [89.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:52sJobs: 1 (f=1): [W(1)] [89.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:51sJobs: 1 (f=1): [W(1)] [89.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:51sJobs: 1 (f=1): [W(1)] [89.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:51sJobs: 1 (f=1): [W(1)] [89.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:51sJobs: 1 (f=1): [W(1)] [89.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:50sJobs: 1 (f=1): [W(1)] [89.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:50sJobs: 1 (f=1): [W(1)] [89.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:50sJobs: 1 (f=1): [W(1)] [90.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:49sJobs: 1 (f=1): [W(1)] [90.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:49sJobs: 1 (f=1): [W(1)] [90.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:49sJobs: 1 (f=1): [W(1)] [90.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:49sJobs: 1 (f=1): [W(1)] [90.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:48sJobs: 1 (f=1): [W(1)] [90.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:48sJobs: 1 (f=1): [W(1)] [90.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:48sJobs: 1 (f=1): [W(1)] [90.6% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:47sJobs: 1 (f=1): [W(1)] [90.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:47sJobs: 1 (f=1): [W(1)] [90.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:47sJobs: 1 (f=1): [W(1)] [90.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:46sJobs: 1 (f=1): [W(1)] [90.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:46sJobs: 1 (f=1): [W(1)] [90.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:46sJobs: 1 (f=1): [W(1)] [91.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:45sJobs: 1 (f=1): [W(1)] [91.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:45sJobs: 1 (f=1): [W(1)] [91.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:45sJobs: 1 (f=1): [W(1)] [91.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:45sJobs: 1 (f=1): [W(1)] [91.3% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 00m:44sJobs: 1 (f=1): [W(1)] [91.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:44sJobs: 1 (f=1): [W(1)] [91.3% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:44sJobs: 1 (f=1): [W(1)] [91.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:43sJobs: 1 (f=1): [W(1)] [91.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:43sJobs: 1 (f=1): [W(1)] [91.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:43sJobs: 1 (f=1): [W(1)] [91.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:42sJobs: 1 (f=1): [W(1)] [91.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:42sJobs: 1 (f=1): [W(1)] [91.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:42sJobs: 1 (f=1): [W(1)] [92.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:41sJobs: 1 (f=1): [W(1)] [92.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:41sJobs: 1 (f=1): [W(1)] [92.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:41sJobs: 1 (f=1): [W(1)] [92.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:40sJobs: 1 (f=1): [W(1)] [92.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:40sJobs: 1 (f=1): [W(1)] [92.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:40sJobs: 1 (f=1): [W(1)] [92.4% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:39sJobs: 1 (f=1): [W(1)] [92.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:39sJobs: 1 (f=1): [W(1)] [92.5% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:39sJobs: 1 (f=1): [W(1)] [92.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:38sJobs: 1 (f=1): [W(1)] [92.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:38sJobs: 1 (f=1): [W(1)] [92.7% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:38sJobs: 1 (f=1): [W(1)] [92.9% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 00m:37sJobs: 1 (f=1): [W(1)] [92.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:37sJobs: 1 (f=1): [W(1)] [92.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:37sJobs: 1 (f=1): [W(1)] [93.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:36sJobs: 1 (f=1): [W(1)] [93.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:36sJobs: 1 (f=1): [W(1)] [93.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:36sJobs: 1 (f=1): [W(1)] [93.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:35sJobs: 1 (f=1): [W(1)] [93.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:35sJobs: 1 (f=1): [W(1)] [93.3% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:35sJobs: 1 (f=1): [W(1)] [93.5% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:34sJobs: 1 (f=1): [W(1)] [93.5% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:34sJobs: 1 (f=1): [W(1)] [93.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:34sJobs: 1 (f=1): [W(1)] [93.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:33sJobs: 1 (f=1): [W(1)] [93.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:33sJobs: 1 (f=1): [W(1)] [93.8% done] [0KB/6150KB/0KB /s] [0/6/0 iops] [eta 00m:33sJobs: 1 (f=1): [W(1)] [94.0% done] [0KB/9225KB/0KB /s] [0/9/0 iops] [eta 00m:32sJobs: 1 (f=1): [W(1)] [94.0% done] [0KB/6150KB/0KB /s] [0/6/0 iops] [eta 00m:32sJobs: 1 (f=1): [W(1)] [94.0% done] [0KB/9216KB/0KB /s] [0/9/0 iops] [eta 00m:32sJobs: 1 (f=1): [W(1)] [94.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:31sJobs: 1 (f=1): [W(1)] [94.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:31sJobs: 1 (f=1): [W(1)] [94.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:30sJobs: 1 (f=1): [W(1)] [94.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:30sJobs: 1 (f=1): [W(1)] [94.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:30sJobs: 1 (f=1): [W(1)] [94.6% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 00m:29sJobs: 1 (f=1): [W(1)] [94.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:29sJobs: 1 (f=1): [W(1)] [94.6% done] [0KB/7168KB/0KB /s] [0/7/0 iops] [eta 00m:29sJobs: 1 (f=1): [W(1)] [94.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:28sJobs: 1 (f=1): [W(1)] [94.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:28sJobs: 1 (f=1): [W(1)] [94.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:28sJobs: 1 (f=1): [W(1)] [95.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:27sJobs: 1 (f=1): [W(1)] [95.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:27sJobs: 1 (f=1): [W(1)] [95.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:27sJobs: 1 (f=1): [W(1)] [95.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:26sJobs: 1 (f=1): [W(1)] [95.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:26sJobs: 1 (f=1): [W(1)] [95.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:25sJobs: 1 (f=1): [W(1)] [95.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:25sJobs: 1 (f=1): [W(1)] [95.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:25sJobs: 1 (f=1): [W(1)] [95.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:24sJobs: 1 (f=1): [W(1)] [95.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:24sJobs: 1 (f=1): [W(1)] [95.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:24sJobs: 1 (f=1): [W(1)] [95.8% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:23sJobs: 1 (f=1): [W(1)] [95.8% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:23sJobs: 1 (f=1): [W(1)] [96.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:22sJobs: 1 (f=1): [W(1)] [96.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:22sJobs: 1 (f=1): [W(1)] [96.0% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:22sJobs: 1 (f=1): [W(1)] [96.2% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:21sJobs: 1 (f=1): [W(1)] [96.2% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:21sJobs: 1 (f=1): [W(1)] [96.2% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:21sJobs: 1 (f=1): [W(1)] [96.4% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:20sJobs: 1 (f=1): [W(1)] [96.4% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:20sJobs: 1 (f=1): [W(1)] [96.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:19sJobs: 1 (f=1): [W(1)] [96.6% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:19sJobs: 1 (f=1): [W(1)] [96.6% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:19sJobs: 1 (f=1): [W(1)] [96.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:18sJobs: 1 (f=1): [W(1)] [96.8% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:18sJobs: 1 (f=1): [W(1)] [96.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:17sJobs: 1 (f=1): [W(1)] [96.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:17sJobs: 1 (f=1): [W(1)] [96.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:17sJobs: 1 (f=1): [W(1)] [97.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:16sJobs: 1 (f=1): [W(1)] [97.1% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:16sJobs: 1 (f=1): [W(1)] [97.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:15sJobs: 1 (f=1): [W(1)] [97.3% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:15sJobs: 1 (f=1): [W(1)] [97.3% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:15sJobs: 1 (f=1): [W(1)] [97.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:14sJobs: 1 (f=1): [W(1)] [97.5% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:14sJobs: 1 (f=1): [W(1)] [97.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:13sJobs: 1 (f=1): [W(1)] [97.7% done] [0KB/8192KB/0KB /s] [0/8/0 iops] [eta 00m:13sJobs: 1 (f=1): [W(1)] [97.7% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:13sJobs: 1 (f=1): [W(1)] [97.9% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:12sJobs: 1 (f=1): [W(1)] [97.9% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:12sJobs: 1 (f=1): [W(1)] [98.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:11sJobs: 1 (f=1): [W(1)] [98.0% done] [0KB/8200KB/0KB /s] [0/8/0 iops] [eta 00m:11sJobs: 1 (f=1): [W(1)] [98.1% done] [0KB/7175KB/0KB /s] [0/7/0 iops] [eta 00m:11sJobs: 1 (f=1): [W(1)] [98.2% done] [0KB/3075KB/0KB /s] [0/3/0 iops] [eta 00m:10sJobs: 1 (f=1): [W(1)] [98.1% done] [0KB/0KB/0KB /s] [0/0/0 iops] [eta 00m:11s]  Jobs: 1 (f=1): [W(1)] [98.1% done] [0KB/0KB/0KB /s] [0/0/0 iops] [eta 00m:11s]
fio-efs: (groupid=0, jobs=1): err= 0: pid=3562: Tue Apr  4 10:12:59 2023
  write: io=10240MB, bw=18068KB/s, iops=17, runt=580363msec
    slat (usec): min=59, max=397, avg=102.94, stdev=20.01
    clat (msec): min=28, max=52662, avg=11332.10, stdev=11879.13
     lat (msec): min=28, max=52662, avg=11332.20, stdev=11879.12
    clat percentiles (msec):
     |  1.00th=[ 1106],  5.00th=[ 2089], 10.00th=[ 2147], 20.00th=[ 2245],
     | 30.00th=[ 2376], 40.00th=[ 2606], 50.00th=[ 2900], 60.00th=[ 3752],
     | 70.00th=[16712], 80.00th=[16712], 90.00th=[16712], 95.00th=[16712],
     | 99.00th=[16712], 99.50th=[16712], 99.90th=[16712], 99.95th=[16712],
     | 99.99th=[16712]
    lat (msec) : 50=0.01%, 100=0.05%, 250=0.12%, 500=0.23%, 750=0.25%
    lat (msec) : 1000=0.24%, 2000=0.95%, >=2000=98.14%
  cpu          : usr=0.14%, sys=0.07%, ctx=17103, majf=0, minf=10
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.2%, 32=0.3%, >=64=99.4%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.1%
     issued    : total=r=0/w=10240/d=0, short=r=0/w=0/d=0, drop=r=0/w=0/d=0
     latency   : target=0, window=0, percentile=100.00%, depth=200

Run status group 0 (all jobs):
  WRITE: io=10240MB, aggrb=18067KB/s, minb=18067KB/s, maxb=18067KB/s, mint=580363msec, maxt=580363msec
[ec2-user@ip-10-0-1-123 ~]$

 
** ddd_v1_w_aEiO_637989@runweb76917:~$  **                                                                                                                                          
                                                                                                                                                                               
##    Submission Report:                                                                                                                                                         
                                                                                                                                                                                                                                                                                                                                                          
    [Executed at: Tue Apr 4 3:16:32 PDT 2023]                                                                                                                                  
                                                                                                                                                                               
    Testing report - The Security Group was created correctly!                                                                                                                 
    Testing report - The EFS file system was created!                                                                                                                          
    Testing report - Flexible IO was run!                                                                                                                                      
                                                                                                                                                                               
    [default]                                                                                                                                                                  
    region = us-east-1                                                                                                                                                         
    gradeFile = /mnt/vocwork2/ccc_v1_g_11ed7_28593/asn1585973_3/asn1585974_1/tmp                                                                                               
    reportFile =/mnt/vocwork2/ccc_v1_g_11ed7_28593/asn1585973_3/asn1585974_1/tmp                                                                                               
    /mnt/vocwork2/ccc_v1_g_11ed7_28593/asn1585973_3/asn1585974_1/tmp/temp_uf_040                                                                                               
    Warning: Permanently added '54.205.23.235' (ECDSA) to the list of known host                                                                                               
    /home/ec2-user/efs/fio-efs-test.img                                                                                                                                        
    Present working directory = /mnt/vocwork2/ccc_v1_g_11ed7_28593/asn1585973_3/                                                                                               
                                                                                                                                                                               
    Default region: us-east-1                                                                                                                                                  
    0                                                                                                                                                                          
