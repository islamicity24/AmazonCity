## What is caching? 
A. A high-speed data storage layer

B. A way to store passwords 

C. A global network for content distribution 

D. An in-memory database 

A. A high-speed data storage layer.

Caching refers to the process of storing frequently accessed data in a high-speed data storage layer so that it can be quickly retrieved when needed, instead of having to be recalculated or fetched from a slower storage layer or external source. This can improve the performance and efficiency of applications and systems that rely on frequently accessed data. Examples of caching include browser caching, where frequently accessed website resources are stored locally on the user's device to reduce page load times, and server-side caching, where frequently accessed data is stored in memory on the server to reduce database or network requests.

![image](https://user-images.githubusercontent.com/126258837/230869562-4a341460-b698-4ab2-9e64-4864fd1a9f21.png)


## Which types of data should you cache? 
A. Data that can be retrieved quickly with simple queries 

B. Dynamically generated web content 

C. Static data that is frequently accessed 

D. Specialized data that is needed by a subset of users 


C. Static data that is frequently accessed.

Data that is static and frequently accessed is the most appropriate type of data to cache. This includes data that does not change frequently and can be precomputed or pre-fetched, such as website resources like images, CSS files, and JavaScript files. By caching this data, you can reduce the number of requests to the underlying data source, improving performance and response times.

Dynamically generated web content, such as personalized web pages, may also benefit from caching, but it depends on the specific use case and how frequently the content changes. Specialized data that is needed by a subset of users may not be appropriate for caching, as it is not frequently accessed by all users and may not provide a significant performance improvement. Data that can be retrieved quickly with simple queries may not need to be cached, as it may already be fast enough to retrieve without caching.

![image](https://user-images.githubusercontent.com/126258837/230872533-b0cf0f18-4c0d-4eb4-a030-71741cf62a6d.png)

## What does Amazon CloudFront enable? 
A. Bidirectional caching between users and an origin host 

B. Multi-tiered and regional caching of content 

C. Transactional processing with an in-memory database 

D. Automatic creation of a time-to-live value 


B. Multi-tiered and regional caching of content.

Amazon CloudFront is a content delivery network (CDN) service provided by Amazon Web Services (AWS) that enables multi-tiered and regional caching of content. It distributes content to servers located in multiple geographic locations around the world, reducing the latency of content delivery and improving performance for end-users.

CloudFront can also be configured to cache content at different levels, including at the edge location closest to the end-user and at the origin server where the content is stored. This enables bidirectional caching between users and the origin host, as well as between the different edge locations in the CDN.

CloudFront does not provide transactional processing with an in-memory database or automatic creation of a time-to-live value.

![image](https://user-images.githubusercontent.com/126258837/230873553-c16f6293-928f-4103-89d1-5884a74792b4.png)


## How does Amazon CloudFront use edge locations? 
A. It caches all content from an origin distribution at the edge location, and delivers the content to clients through the fastest edge location. 

B. It caches local content at edge locations. It delivers the cached content to clients through the edge location that requires the fewest network hops to reach those clients. 

C. It caches frequently accessed content at edge locations. It delivers the cached content to clients through the edge location with the lowest latency to those clients. 

D. It caches Regional data at Regional edge locations, and delivers the content to clients through their Regional edge locations. 

C. It caches frequently accessed content at edge locations. It delivers the cached content to clients through the edge location with the lowest latency to those clients.

Amazon CloudFront uses edge locations to cache frequently accessed content as close to the end-user as possible, reducing the latency of content delivery and improving performance. When a client requests content that has been cached at an edge location, CloudFront delivers the content to the client through that edge location, which typically has the lowest latency to the client.

CloudFront also uses a variety of algorithms and metrics to determine the optimal edge location for delivering content, including the location of the client, the latency of network connections, and the availability and capacity of the edge locations. This ensures that clients receive content from the fastest and most available edge location for their specific location and network conditions.

CloudFront does not cache all content from an origin distribution at the edge location, as this could result in unnecessary storage and resource utilization. Instead, it selectively caches frequently accessed content. It also does not cache local content at edge locations based on geographic proximity alone, as this may not result in the best performance for all clients.

![image](https://user-images.githubusercontent.com/126258837/230874251-892c1a34-d175-4f73-b82e-894175fd4efc.png)


## Where is application session data cached when using sticky sessions? 

A. Web server 

B. Web browser 

C. Elastic Load Balancing load balancer 

D. Amazon CloudFront 

C. Elastic Load Balancing load balancer. ( X SALAH )

When using sticky sessions, application session data is cached on the Elastic Load Balancing (ELB) load balancer. Sticky sessions are a technique used in load balancing to route requests from the same client to the same server, based on a unique identifier, such as a session ID. This enables the server to maintain session state and provide a consistent experience for the client across multiple requests.

When a client sends a request to an ELB load balancer that is configured for sticky sessions, the load balancer identifies the target server based on the session ID and routes the request to that server. The server then retrieves the cached session data from the load balancer and uses it to process the request. The session data is stored in memory on the load balancer and is typically backed up to persistent storage for fault tolerance.

Application session data is not cached on the web server, web browser, or Amazon CloudFront when using sticky sessions, as these components are not directly involved in the load balancing and routing of requests. However, the web server may still maintain session state in memory or in a database, depending on the specific implementation of the application.

![image](https://user-images.githubusercontent.com/126258837/230876252-6dfa1ce4-04e0-48ea-aa3a-de54972f3f03.png)


## Which statement best describes an efficient way to deliver on-demand streaming content by using Amazon CloudFront? 

A. CloudFront does not work with streaming content. 

B. A best practice is to create separate origin servers for each Region where you serve streaming content.

C. A best practice is to create distributions for each Region where you serve streaming content.

D. A best practice is to create video segments and store them in an Amazon Simple Storage Service (Amazon S3) bucket. Then, use CloudFront to cache the segments. 

D. A best practice is to create video segments and store them in an Amazon Simple Storage Service (Amazon S3) bucket. Then, use CloudFront to cache the segments.

When delivering on-demand streaming content, a best practice is to create video segments and store them in an Amazon S3 bucket. These segments are typically several seconds long and can be delivered to viewers in any order, allowing for more efficient delivery and reducing the impact of network disruptions.

To deliver these segments efficiently, you can use Amazon CloudFront to cache the segments at edge locations around the world. When a viewer requests a segment, CloudFront can deliver the segment from the edge location closest to the viewer, reducing latency and improving performance.

Creating separate origin servers for each Region or creating distributions for each Region can also be effective strategies for delivering on-demand streaming content with CloudFront, as this can further reduce latency and improve performance for viewers in those regions.

Option A is incorrect, as CloudFront can deliver streaming content efficiently. Option B is a valid strategy but may not be necessary depending on the specific use case. Option C is also a valid strategy, but it is not the only or most efficient way to deliver on-demand streaming content with CloudFront.

![image](https://user-images.githubusercontent.com/126258837/230876998-7d5a30c6-9248-4742-86f5-37a75e5408e1.png)


## What is Amazon DynamoDB Accelerator (DAX)? 

A. A fully managed, highly available, in-memory cache for DynamoDB 

B. A feature of DynamoDB that automatically adjusts read/write capacity to handle load 

C. A fully managed, highly available cache that is backed by DynamoDB 

D. A feature of DynamoDB that enables fast lookup of items by using secondary keys 

A. Amazon DynamoDB Accelerator (DAX) is a fully managed, highly available, in-memory cache for DynamoDB.

Amazon DynamoDB Accelerator (DAX) is a fully managed, highly available, in-memory cache for DynamoDB that delivers up to a 10x performance improvement for read-heavy applications. DAX provides a caching layer in front of a DynamoDB table, enabling applications to read data from the cache, rather than from the table itself.

DAX automatically manages cache invalidation, ensuring that data in the cache is consistent with the data in the underlying DynamoDB table. DAX integrates with the DynamoDB API, so existing DynamoDB applications can use DAX without making any code changes.

Option B is incorrect, as automatic scaling is a different feature of DynamoDB that adjusts read/write capacity to handle load. Option C is also incorrect, as DAX is not a cache that is backed by DynamoDB, but rather a cache that sits in front of DynamoDB. Option D is also incorrect, as fast lookup of items by secondary keys is a feature of DynamoDB itself, not DAX.

![image](https://user-images.githubusercontent.com/126258837/230877517-f6411fe4-e67c-4ad4-a736-28bdc8539dbc.png)


## 9. How can an application use Amazon ElastiCache to improve database read performance? (Select TWO.) 

A. Read data from the database first and write the most frequently read data to ElastiCache. 

B. Direct all read requests to the database and configure it to read from ElastiCache when a cache miss occurs. 

C. Read data from ElastiCache first and write to ElastiCache when a cache miss Occurs. 

D. Write data to ElastiCache whenever the application writes to the database. 

E. Replicate the database in ElastiCache, and direct all reads to ElastiCache and all writes to the database.

A and C ( X SALAH) An application can use Amazon ElastiCache to improve database read performance by:

A. Reading data from the database first and writing the most frequently read data to ElastiCache.
By doing this, the application can quickly retrieve frequently accessed data from the cache, rather than querying the database every time.

C. Reading data from ElastiCache first and writing to ElastiCache when a cache miss occurs.
This approach, known as cache-aside, involves checking the cache for the requested data first. If the data is not found in the cache, the application queries the database for the data and stores it in the cache for future requests.

Option B is incorrect, as it suggests directing all read requests to the database and only reading from ElastiCache on a cache miss, which is not an effective use of the cache.

Option D is incorrect, as writing data to ElastiCache every time the application writes to the database would not necessarily improve read performance, as the cache is not being used for reads.

Option E is also incorrect, as replicating the entire database in ElastiCache is not a practical approach, and would not be effective for improving read performance.

## Which role does Amazon CloudFront play in protecting against distributed denial of service (DDoS) attacks? 

A. Routes traffic through edge locations 

B. Controls traffic by the source IP addresses of requests 

C. Restricts traffic by geography to help block attacks that originate from specific countries 

D. Performs deep packet inspection to detect attacks 

A. Amazon CloudFront plays a role in protecting against distributed denial of service (DDoS) attacks by routing traffic through edge locations. This can help to distribute the attack traffic across multiple locations and reduce the impact of the attack. Additionally, CloudFront can use features like AWS Shield and AWS WAF to provide additional protection against DDoS attacks, such as by blocking traffic from known malicious IP addresses or by using rate limiting to control traffic. While CloudFront can provide some protection against DDoS attacks, it is important to use a combination of security measures to fully protect against these types of attacks.

![image](https://user-images.githubusercontent.com/126258837/230878939-1d428878-b15d-4936-888a-6d188cb473ad.png)





![image](https://user-images.githubusercontent.com/126258837/230878115-e454456d-044b-4b8b-864f-a5e1016268e7.png)















