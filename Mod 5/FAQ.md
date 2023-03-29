## 1. Which use cases indicate tha a non-relational database might be a better solution than a relational database? 
1. Horizontal scaling for massive data volume 
2. ACID compliance for all database transakstion 
3. Data with unpredictable attributes 
4. Strong read after-write consistency 
5. High availability and fault tolerance

There are several use cases where a non-relational database might be a better solution than a relational database. These include:

**Horizontal scaling for massive data volume**: Non-relational databases are designed to handle massive amounts of data and can be easily scaled horizontally to accommodate growing volumes of data. This makes them a better solution for applications that require high scalability and performance.

**Data with unpredictable attributes**: Non-relational databases are schema-less, which means that they can store data with unpredictable attributes. This makes them a better solution for applications that deal with unstructured or semi-structured data.

**Strong read after-write consistency**: Some non-relational databases provide strong consistency guarantees for read-after-write operations, which ensures that data is always up-to-date and consistent across multiple nodes in the database cluster. This makes them a better solution for applications that require strong consistency guarantees.

**High availability and fault tolerance**: Non-relational databases are designed to be highly available and fault-tolerant, which means that they can continue to function even if some of the nodes in the database cluster fail. This makes them a better solution for applications that require high availability and reliability.

In contrast, relational databases are generally better suited for applications that require ACID compliance for all database transactions. ACID compliance guarantees that database transactions are processed reliably and consistently, which makes relational databases a better solution for applications that require strict transactional integrity. However, this can also make them less flexible and less scalable than non-relational databases.


## Which examples are good use cases for Amazon Relational Database Service RDS ?

Amazon Relational Database Service (RDS) is a fully-managed database service offered by AWS. It supports various relational database engines like MySQL, PostgreSQL, Oracle, SQL Server, and MariaDB. Here are some good use cases for Amazon RDS:

**Web applications**: Amazon RDS can be used to store data for web applications. You can use the service to store user profiles, session data, and other application data. With Amazon RDS, you don't have to worry about managing the underlying database infrastructure, as AWS takes care of that for you.

**E-commerce applications**: Amazon RDS can be used to store data for e-commerce applications. You can use the service to store product information, customer data, and order information. With Amazon RDS, you can ensure that your data is secure, highly available, and scalable.

**Content management systems (CMS)**: Amazon RDS can be used to store data for content management systems like WordPress, Drupal, and Joomla. You can use the service to store content, user data, and other application data. With Amazon RDS, you can ensure that your data is secure, highly available, and scalable.

**Analytics and reporting**: Amazon RDS can be used to store data for analytics and reporting. You can use the service to store data from multiple sources, and then use a reporting tool to analyze the data. With Amazon RDS, you can ensure that your data is secure, highly available, and scalable.

**Mobile applications**: Amazon RDS can be used to store data for mobile applications. You can use the service to store user profiles, session data, and other application data. With Amazon RDS, you can ensure that your data is secure, highly available, and scalable.

Overall, Amazon RDS is a good fit for any application that requires a reliable, scalable, and fully-managed relational database service.


## 9. A company wants to migrate their on-premises Oracle database to amazon Aurora MySQL. Which proess describe the high level steps ?
