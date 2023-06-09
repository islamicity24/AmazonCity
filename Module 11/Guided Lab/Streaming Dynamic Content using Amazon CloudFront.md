# Module 11 - Guided Lab: Streaming Dynamic Content using Amazon CloudFront

Lab overview and objectives
In this lab, you will use Amazon CloudFront to deliver a dynamic, multiple bit-rate stream to a connected device using Apple’s HTTP Live Streaming (HLS) protocol. The stream can be played on any browser that supports the HLS protocol. In this lab, you will also use Amazon Elastic Transcoder to convert a source video into multiple bit-rates that will be delivered using CloudFront.

After completing this lab, you should be able to:

Create multiple bit-rate versions of a given source media file using Amazon Elastic Transcoder.

Use Amazon CloudFront to deliver the dynamic (multi bit-rate) stream created by Amazon Elastic Transcoder.

 

At the end of this lab, your architecture will look like the following example:

Architecture

 

##Duration

This lab will require approximately 30 minutes to complete.


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


Task 1: Lab Preparation
In this lab, you will be using a sample video file to configure a dynamic stream. For your convenience, an Amazon Simple Storage Service (Amazon S3) bucket has already been created.

In the AWS Management Console, on the Services menu, choose S3.

An S3 bucket containing the string awstrainingreinvent should be present. Note the Region that the bucket is in, and open the bucket.

Open the input folder. It contains a video file named AmazonS3Sample.mp4.

Note: From the time you log in to the Amazon S3 console, it can take up to ten minutes for the file to appear in the S3 bucket. If you do not see it, select the circular arrow icon on the upper right of the screen to refresh the contents of the bucket.


Task 2: Create an Amazon CloudFront Distribution
In this task, you will create an Amazon CloudFront distribution that will be used to deliver the multiple bit-rate files generated by Amazon Elastic Transcoder to end-user devices.

On the Services menu, choose CloudFront.

Choose Create Distribution.

Under the Web section of the page, choose Get Started.

Under Origin Settings section of the page, enter the follow information:

Select the Origin Domain Name field. A list of S3 buckets will appear. Choose the one that was created earlier that has awstrainingreinvent as part of the file name.
Under Restrict Bucket Access select No.
Scroll to the bottom of the page, then choose Create Distribution.


Task 3: Create an Amazon Elastic Transcoder Pipeline
Create a Pipeline
In this section, you will create a pipeline that will manage the jobs to transcode the input file.

In the AWS Management Console, on the Services menu, choose  Elastic Transcoder.

In the navigation bar of the Amazon Elastic Transcoder console, select the same Region that the S3 bucket was created in.

On the Pipelines page, choose Create a new Pipeline.

For Pipeline Name, enter InputPipeline

For Input Bucket, select the awstrainingreinvent S3 bucket.

For IAM Role, under Other roles, select AmazonElasticTranscoderRole. This is a role that was pre-created in this lab's CloudFormation template that uses the managed policy AmazonElasticTranscoderRole. The Elastic Transcoder service will assume this role to access Amazon S3 and Amazon Simple Notification Service (Amazon SNS) resources in your lab account.

In the Configuration for Amazon S3 Bucket for Transcoded Files and Playlists section, enter the follow information:

Under Bucket, select the awstrainingreinvent S3 bucket.
Under Storage Class, select Standard.
In the Configuration for Amazon S3 Bucket for Thumbnails section, enter the following information:

Under Bucket, select the awstrainingreinvent S3 bucket.
Under Storage Class, select ReducedRedundancy.
Choose Create Pipeline.


Create a Job
In this section, you will create a job under the Amazon Elastic Transcoder pipeline that was just created. The job does the work of transcoding the input file into multiple bit-rates as selected.

On the Pipelines page, choose Create New Job to create a transcoding job. You create the job in the pipeline (queue) that you want to use to transcode the video file.

For Pipeline, select InputPipeline.

For Output Key Prefix, enter output/.

Amazon Elastic Transcoder will prepend this value to the names of all files that the job will create (including output files, thumbnails, and playlists).

For Input Key, select the input file labeled input/AmazonS3Sample.mp4.


Configure Output Details
The settings in this section will determine how many output files (bit-rates) are created. You will configure three output files for this demo having three separate bit-rates (2Mbps, 1.5Mbps and 1Mbps). Each output bit-rate will require you to create a separate output details section. This will also output a playlist file for each bit-rate, which lists all the segments that make up the stream.

For Preset:, select System preset: HLS 2M

For Segment Duration, enter 10 (which is the HLS default).

For Output Key, enter the unique prefix HLS20M to name the segments created using this preset.

Click + Add Another Output and repeat the steps above to generate segments for presets HLS 1.5M and HLS 1M and then provide the respective prefix names:

HLS15M
HLS10M
 Caution: Do not create the job yet! Instead, complete the next few steps in this lab which will have you add a playlist to the job.


Configure a Playlist
The playlist will combine all the individual bit-rate playlists and provide a single URL for the devices to playback the stream. To configure a playlist, do the following:

Under Playlists (Adaptive Streaming), choose Add Playlist, then configure:

Playlist Name primary
Playlist Format: HLSv3
Select all the three outputs, which were entered in the previous section, to include them in this  playlist by selecting the + option.

Choose Create New Job.

The transcoding process should complete within a minute.


Task 4: Test Playback of the Dynamic (Multi Bit-Rate) Stream
In this module, you will test the playback of the dynamic stream generated in the previous section using an iOS or Android device. You can also use an Android 4.x device to test the below exercise.

Note Certain browsers may not support this feature. Use the default web browser in the device to test.


Construct the Playback URL
The playback URL that plays through Amazon CloudFront is comprised of two components:

Amazon CloudFront domain name

Path of the playlist file in the S3 bucket (output generated by Elastic Transcoder):

http://<CloudFront domain name>/<playlist file path in Amazon S3 bucket>

Obtain an Amazon CloudFront Domain Name
To obtain an Amazon CloudFront domain name:

In the AWS Management Console, on the Services menu, choose CloudFront.
Select the Amazon CloudFront distribution that was previously created, and verify that the Distribution Status has changed from InProgress to Deployed.
Proceed to the next step only after the Status changes to Deployed.
Select the Distribution by selecting the square box to the left of the Distribution.
Choose Distribution Settings.
Copy the Domain Name and paste it into a text editor.

Obtain the Playlist File Path
To obtain the playlist file path:

On the Services menu, choose S3.

Select the awstrainingreinvent S3 bucket.

Open the output folder (which contains the output of the transcoding job) and select the primary.m3u8 playlist file.

This is the file that you will play on your mobile device.

Next, you must create the URL to the file from CloudFront.

In a text editor, construct the URL by appending /output/primary.m3u8 to the end of your CloudFront domain name.

The new URL should look similar to: d1ckwesahkbyvu.cloudfront.net/output/primary.m3u8

Type the URL into the default browser of an iOS or Android device. If you do not have a mobile device available, type the URL into a browser on your computer.

  Be aware that standard data rates may apply when playing the video on a mobile device.

The stream should start playing on your device and dynamically request the relevant segments based on your bandwidth and CPU conditions.

You have learned how to use AWS services such as Amazon S3, Amazon Elastic Transcoder, and Amazon CloudFront together to deliver HLS media files to iOS or Android devices.

You have successfully:

Learned the basic concepts and terminology of the Amazon Elastic Transcoder and Amazon CloudFront services.
Created your own Amazon Elastic Transcoder pipeline and Amazon CloudFront distribution.
Used Amazon Elastic Transcoder to transcode a video file into different HLS formats and distributed it to remote devices using Amazon CloudFront.

Submitting your work
At the top of these instructions, choose Submit to record your progress and when prompted, choose Yes.

If the results don't display after a couple of minutes, return to the top of these instructions and choose Grades

Tip: You can submit your work multiple times. After you change your work, choose Submit again. Your last submission is what will be recorded for this lab.

To find detailed feedback on your work, choose Details followed by  View Submission Report.


Lab complete 
 Congratulations! You have completed the lab.

Choose End Lab at the top of this page, and then select Yes to confirm that you want to end the lab.

A panel indicates that DELETE has been initiated... You may close this message box now.

Select the X in the top right corner to close the panel.


Additional Resources
Amazon Elastic Transcoder and Amazon CloudFront
Amazon CloudFront
AWS Training and Certification
 
 

     Submission Report:                                                                                  
                                                                                                        
                                                                                                        
    [Executed at: Wed Apr 5 4:00:32 PDT 2023]                                                           
                                                                                                        
    Testing report - A CloudFront distribution was created.                                             
    Testing report - The Pipeline was created.                                                          
    Testing report - The correct storage class was chosen fo                                            
    Testing report - The correct storage class was chosen fo                                            
    "Testing report - PROBLEM: The Output Key Prefix is not                                             
    er."                                                                                                
    Testing report - PROBLEM: At least one of the selected p                                            
                                                                                                        
                                                                                                        
    gradeFile = /mnt/vocwork2/ccc_v1_g_11ed7_28593/asn158599                                            
    reportFile =/mnt/vocwork2/ccc_v1_g_11ed7_28593/asn158599                                            
    /mnt/vocwork2/ccc_v1_g_11ed7_28593/asn1585997_15/asn1585                                            
    len 4                                                                                               
    Present working directory = /mnt/vocwork2/ccc_v1_g_11ed7                                            
rk                                                                                                      
                                                                                                        
    Default region: us-east-1
