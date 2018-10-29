# 3. EC2

### RDS 101

Relational Database Service



What is a Relational Database?

* Tables, rows, fields (columns) etc.



Relational Database Types instances you can provision within RDS

* SQL Server
* Oracle
* MySQL Server
* PostgreSQL
* Aurora (AWS flagship technology)
* MariaDB



Non relational Databases

* Database
  * Collection (basically a table)
  * Document (like a row in a table)
  * Key-value pairs (like fields)



What is Data Warehousing?

* Used for business intelligence. Tools like Congnos, Jaspersoft, SQL Server Reporting Services, Oracle Hyperion, and SAP NetWeaver 
* Used to pull in very large and complex data sets. Usually used by management to do queries on data (such as current performance vs targets etc.)



OLTP vs OLAP

* Online Transaction Processing (OLTP) differs from Online Analytics Processing (OLAP) in terms of the types of queries you will run
  * OLTP are your standard queries
  * OLAP are usually far more complex 



What is Elasticache?

* This is a web service that makes it easy to deploy, operate, and scale an in-memory cache in the cloud. 
* The service improves the performance of web applications by allowing you to retrieve information from fast, managed, in-memory caches instead of relying entirely on slower disk-based databases
* Elasticache supports two open-source in-memory caching engines:
  * Memcached
  * Redis



AWS Database Types - Summary

* RDS - OLTP	
  * SQL, MySQL, PostgreSQL, Oracle, Aurora, MariaDB
* DynamoDB - No SQL
* RedShift - OLAP
* Elasticache - In-memory caching:
  * Memcached
  * Redis



### RDS - Back Ups, Multi-AZ and Read Replicas

There are two types of Backups for AWS: **Automated Backups and Database Snapshots**

* Automated backups allow you to recover your database to any point in time within a "retention period".
  * the retention period can be between one and 35 days
  * automated backups will take a full daily snapshot and will also store transaction logs throughout the day
* When you do a recovery, AWS will first choose the most recent daily backup and then apply transaction logs relevant to that day - this allows you to do a point in time recovery down to a second, within the retention period
* Automated backups are enabled by default
  * the backup data is stored in S3 and you get free stroage space equal to the size of your database (so if you have an RDS instance of 10gb, you will get 10gb worth of storage)
* Backups are taken within a defined window
  * during the backup window, storage I/O may be suspended while your data is being backed up and you may experience elevated latency
* Snapshots are done manually (i.e. they are user initiated)
  * they are stored even after you delete the original RDS instance, unlike automated backups
* Restoring backups
  * Whenever you restore either an automatic backup or a manual snapshot, the restored version of the database will be a new RDS instance with a **new DNS endpoint**
* Encryption
  * Encryption at rest is supported for MySQL, ORacle, SQL Server, PostgreSQL, MariaDB & Aurora
  * Encryption is done using the AWS Key Management Service (KMS)
  * Once your RDS instance is encrypted, the data stored at rest in the underlying storage is encrypted, as are its automated backups, read replicas and snapshots
  * At the present time, encrypting an existing DB instance is not supported
  * **To use RDS encryption for an existing database, you must first create a snapshot, make a copy of that snapshot and encrypt the copy**



What is Multi-AZ?

* Allows you to have an exact copy of your production database in another AZ
* AWS handles the replication for you, so when your production DB is written to, this write will automatically be synchronized to the standby database
* In the event of a planned db maintenance, DB Instance failure, or an AZ failure, Amazon RDS will automatically failover to the standby so that database operations can resume quickly without administrative intervention
* **It is for disaster recovery only!**
  * It is not primarily used for improving performance --> performance improvements would have you need Read Replicas
* Available for the following DBs:
  * SQL Server
  * Oracle
  * MySQL Server
  * PostgreSQL
  * MariaDB
  * Aurora is there by default
* Multi-AZ is synchronous, Read Replicas are async



What is a Read Replica?

* Allow you to have a read-only copy of your production database
* Achieved by using an async replication from the primary RDS instance to this read replica
* You would use read replicas primarily for very read-heavy database workloads
* You can have up to 5 read replicas per database 
* You can also have read replicas of read replicas (but be careful of latency here)
  * Each read replica will have it's own DNS endpoint
* You can have read replicas that have multi-AZ
* You can create read replicas of multi-AZ source databases
* It is a way of **improving performance** and**scaling out** your database so different EC2 instances for example can read from different replicas
  * takes the load off of the one db
* Available for the following dbs:
  * MySQL Server
  * PostgreSWL
  * MariaDB
  * Aurora
* Used for scaling, not disaster recovery (use multi-AZ for this)!
* You need to have automatic backups turned on in order to deploy a read replica
* Read replicas can be promoted to become their own databases, but this breaks the replication
* You can have a read replica in a separate region



### Elasticache 101

What is it?

* Elasticache is a web service that makes it easy to deploy, operate and scale an in-memory cache in the cloud
* The service improves the performance of web applications by allowing you to retrieve information from fast, managed, in-memory caches instead of relying entirely on slower disk-based databases 
* Can be used to **significantly improve latency and throughput** for many **read-heavy application workloads** (such as social networking, gaming, media sharing and Q&A portals) **or compute-intensive workloads** (such as recommendation engines)
* Caching improves application performance by storing critical pieces of data in memory for low-latency access
* Cached information may include the results of I/O intensive database queries or the results of computationally-intensive calculations



Types of Elasticache

* Memcached
  * a widely adopted memory object caching system
  * Elasticache is protocol compliant with Memcached, so popular tools that you use today with existing Memcached environments will work seamlessly with the service
  * Use this one if you are not concerned with redundancy
* Redis
  * a popular open-source in-memory key-value store that supports data structures such as sorted sets and lists
  * Elasticache supports Master / Slave replication and multi-AZ which can be used to achieve cross AZ redundancy
* Although both appear similar on the surface (in that they are both in-memory key stores), they are actually quite different in practice
  * Because of the replication and persistence features of Redis, Elasticache manages Redis more as an RDB
    * Redis Elasticache clusters are managed as stateful entities that include failover, similar to how Amazon RDS manages database failover
  * Because Memcached is designed as a pure caching solution with no persistence, Elasticache manages Memcached nodes as a pool that can grow and shrink, similar to an Amazon EC2 Auto Scaling Group
    * Individual nodes are expendable, and Elasticache provides additional capabilities here, such as automatic node replacement and auto discovery



Memcached Use Cases

* Is object caching your primary goal, for example to offload your database? If so, use Memcached
* Are you interested in as simple a caching model as possible? If so, use Memcached
* Are you planning on running large cache nodes, and require multithreaded performance with utilization of multiple cores? If so, use Memcached
* Do you want the ability to scale your cache horizontally as you grow? If so, use Memcached



Redis Use Cases

* Are you looking for more advanced data types, such as lists, hashes and sets? If so, use Redis
* Does sorting and ranking datasets in memory help you, such as with leaderboards? If so, use Redis
* Is persistence of your key store important? If so, use Redis
* Do you want to run in multiple AWS Availability Zones with failover? If so, use Redis



Elasticache is a good choice if your database is particularly read-heavy and not prone to frequent changing

* Redshift is a good answer if the reason your database is feeling stress is because management keep running OLAP transactions on it etc. (Good answer if it is more of a data warehousing question)



Use Memcached if:

* Object caching is your primary goal
* You want to keep things as simple as possible
* You want to scale your cache horizontally (scale out)

Use Redis if:

* You have advanced data types, such as lists, hashes and sets
* You are doing data sorting and ranking (such as leader boards)
* Data Persistence
* Multi AZ
* Pub/Sub capabilities are needed



# 4. S3

### S3 101

* Secure, durable, highly-scalable **object storage**
  * files can be 0 bytes to 5TB
  * unlimited storage
  * Files are stored in buckets (similar to a folder)
* Data is spread across multiple devices and facilities
* S3 has a universal namespace, so the name of a bucket must be unique globally 
* Upon uploading a code to S3, you will receive a HTTP 200 code to indicate that the upload was successful

Data Consistency Model for S3

* **Read after Write consistency** for PUTS of new Objects
* **Eventual Consistency** for overwrite PUTS and DELETES (can take some time to propagate)

S3 is a simple key-value store

* Key (this is simply the name of the object)
* Value (this is simply the data, which is made up of a sequence of bytes)
* Version ID (Important for versioning)
* Metadata (data about the data you're storing - you can add your own metadata)
* Sub resources - bucket specific config: things like Bucket Policies, ACLs, Transfer Acceleration (speeds up uploads to S3) and CORS

The Basics:

* Built for 99.99% availability for the S3 platform
* Amazon guarantee 99.9% availability 
* Amazon guarantee 99.999999999% durability for S3 information
* Tiered storage is available
* Lifecycle management
* Versioning
* Encryption
* Secure data using ACLs and Bucket policies



### Storage Tiers

* Normal S3: 99.99% availability, 99.999999999% durability, stored redundantly across multiple devices in multiple facilities and is designed to sustain the loss of 2 facilities concurrently
* S3-IA (Infrequently Accessed): For data that is accessed less frequently, but requires rapid access when needed. Lower fee than S3, but you are charged a retrieval fee for any object retrieved
* S3 - One Zone IA: Same as IA however data is stored in a single AZ only, still 99.999999999% durability, but only 99.5% availability. Cost is 20% less than regular S3-IA (Not resilient to the loss of the AZ)
* Reduced Redundancy Storage: Designed to provide 99.99% durability and 99.99% availability of objects over a given year. Used for data that can be recreated if lost e.g. thumbnails (This feature is starting to disappear from AWS docs). Used to be cheapest but AWS now recommend you use regular S3
* Glacier: Very cheap but for archival only. It is optimized for data that is infrequently accessed as it takes 3-5hrs to restore from Glacier

Charged for:

* Storage per GB
* Requests (Get, Put, Copy, etc.)
* Storage management pricing
  * inventory, analytics, and object tags
* Data management pricing
  * data transferred out of S3
* Transfer acceleration (uses CloudFront)



### S3 Security

* By default, all newly created buckets are PRIVATE
  * You can set up access control to your buckets using:
    * Bucket policies: applied at a bucket level
    * Access Control Lists: Applied at an object level
  * S3 buckets can be configured to create access logs, which log all requests made to the S3 buckets. These logs can be written to another bucket

### S3 Policies

* Types of encryption available: AES-256 and AWS-KMS which are both server-side encryption 
* By default, only the owner has read & write access
* ACLs are at an object level rather than a bucket level
* Bucket Policies are at a bucket level, written in JSON



### S3 Encryption

* IN transit encryption
  * Done using SSL/TLS,  typically HTTPS to access files
* At rest encryptions
  * Server side encryptions:
    * S3 managed keys (**SSE-S3**): Each object is encrypted with it's own unique key using strong multi-factor auth with the key used for encrypting also encrypted with a master key. Sometimes called AES-256
    * AWS Key Management Service, Managed Keys, **S3-KMS**
      * Comes with an audit trail and an envelope key, you then have that view of the key use
    * Server side encryption with customer provided keys - **SSE-C**
      * You are in charge of the keys. AWS manage the encryption and decryption, but you are required to manage the rest, including things like key rotations etc.
  * Client Side encryption
    * you do this before upload
* Enforcing encryption on S3 buckets
  * Every time a file is uploaded to S3, a PUT request is initiated
  * IF the file is to be encrypted at uploading time, the **x-amz-server-side-encryption parameter** will be included in th request header
  * Two options are currently available:
    * **x-amz-server-side-encryption: AES256 ** (SSE-S3 - S3 managed keys)
    * **x-amz-server-side-encryption: ams:kms** (SSE-KMS - KMS managed keys)
  * When this param is included in the header of the PUT request, it tells S3 to encrypt the object at the time of upload, using the specified encryption method
  * You can enforce the use of Server Side Encryption by using a bucket policy which denies any S3 PUT request which does not include the **x-amz-server-side-encryption** parameter in the request header



CORS controls access from one resource to another



### CloudFront

* A content delivery network (CDN) is a system of distributed servers (network) that deliver webpages and other web content to a user based on the geographic locations of the user, the origin of the webpage, and a content delivery server
  * Basically a web service that speeds up the delivery of your static and dynamic web content 
  * About enabling better performance regardless of where you are in the world
* Makes use of edge locations that keep a cache of the data that can be requested from. Requests therefore only have to travel to the edge location
* TTLs (max is 1yr, default is 24hrs, all done in s) are setup to clear the cache every so often (manual clears will cost)
* Key Terminology
  * **Edge Location** - this is the location where content is cached and can also be written. Separate to an AWS Region/AZ (way more edge locations)
  * **Origin** - This is the origin of all the files that the CDN will distribute. Origins can be an S3 bucket, an EC2 instance , an Elastic Load Balancer, or Route53
  * **Distribution** - This is the name given to the CDN, which consists of a collection of Edge Locations
  * **Web Distribution** - typically used for websites
  * **RTMP** - used for media streaming

What is CloudFront?

* Amazon CloudFront can be used to deliver your entire website, including dynamic, static, streaming and interactive content using a global network of edge locations
* Requests for your content are automatically routed to the nearest edge location, so content is delivered with the best possible performance
  * e.g. can be used to optimize performance for users accessing a website backed by S3
* Optimized to work with other AWS services, like S3, EC2, Elastic Load Balancing, and Route53. It also works seamlessly with any non-AWS  origin server, which stores the original, definitive versions of your files

CloudFront and S3 Transfer Acceleration

* Amazon S3 Transfer Acceleration enables fast, easy and secure transfers of files over long distances between your end users and S3 bucket
* Transfer Acceleration takes advantage of Amazon CloudFront's globally distributed edge locations. As the data arrives at an edge location, date is routed to Amazon S3, over an optimized network path

Signed URLS / cookies ensure that only some people can access pages

###### Exam Tips

* Edge locations are not just READ only - you can WRITE to them too
* Objects are cached for the life of the TTL
* You can clear cached objects yourself at a cost (Called an invalidation)
* You can restrict bucket access for requests to only come through CloudFront



### S3 Performance Optimization

S3 is designed to support very high request rates. However, if your S3 buckets are routinely receiving >100 PUT / LIST / DELETE or >300 GET requests per second, then there are some best practice guidelines that will help optimize S3 performance



Guidance is based on the type of workload that you are running:

* **GET-Intensive Workloads -** use CloudFront content delivery service to get best performance. CloudFront will cache your most frequently accessed objects and will reduce latency for your GET requests
* **Mixed Request Type Workloads -** a mix of GET, PUT, DELETE, GET Bucket - the key names that you use for your objects can impact performance for intensive workloads
  * S3 uses the key name to determine which partition an object will be stored in
  * The use of sequential key names e.g. names prefixed with a timestamp or alphabetical sequence increases the likelihood of having multiple objects stored on the same partition
  * For heavy workloads, this can cause I/O issues an contention
  * By using a random prefix to key names, you can force S3 to distribute your keys across multiple partitions, distributing the I/O workload



###### Optimization Summary

* remember the 2 main approaches;
  * GET-intensive workloads - use CloudFront
  * Mixed-workloads - avoid sequential key names for your S3 objects. Instead, add a random prefix like a hex hash to the key name to prevent multiple objects being stored on the same partition



S3 Performance Updates

* In July 2018, Amazon announced a massive performance increase in S3 that completely negates the need for randomness to be included in the object keynames - any names can be used (even sequential) without any performance implication
* S3 can now support 3500 put requests per second and 5500 get requests





READ THE S3 FAQ!!!!!!!!





# 5. Intro to Serverless Computing

### Brief history

Data Centres --> IaaS --> PaaS --> Containers --> Serverless

![Screen Shot 2018-08-06 at 4.58.20 PM](/Users/brandonpickup/Desktop/Screen Shot 2018-08-06 at 4.58.20 PM.png)

* Iaas
  * Able to comission a machine anywhere in the world with nothing more than an API call
  * Issue is that there is still infrastructure to runa nd hence something can still gowrong - offline/hack/etc.
* PaaS
  * Amazons version is elastic beanstalk
  * Still a server that you needed to run and could go wrong
* Containers
  * Lightweight alternatives to full blown virtualisation
  * still ened to be deployed to a server, need to worry about keeping them running and scaling them to deal with response to load
* Serverless
  * Lambda came along and allows you to take your code and run it without any need to provision servers, install servers, deal with provisioning and managing lower level infrastructure without the developer needing to worry about doing that
  * No worry about having to manage servers, Complete game changer



### Lambda

What is lambda?

* functions that are triggered through the API gateway
* it is the latests abstraction layer
* removes worry about data centres / hardware / assembly cide / protocols / high level languages /OSs etc. AWS takes care of it all



Actual definition:

* is a compute service where you can upload your code and create a Lambda function. AWS lambda takes care of provisioning and managing the servers that you use to run the code. you do not have to worry about patching, scaling, etc. 

* Can be used in the follwoing ways:

  * As an event-driven compute service where AWS Lambda runs your code in response to events. These events could be changes to data in an Amazon S3 bucket or an Amazon DynamoDB table
  * As a compute service to run your code in response to HTTP requests using Amazon API Gateway calls made using AWS SDKs

  

Languages supported by lambda

* Node.js

* C#

* Java

* Python

* Go

  

Priced

* On the number of requests
  * the first 1million request are free
  * $0.20 per 1million requests thereafter
* On duration
  * Calculated from the time your code begins executing until it returns or otherwise terminates, rounded up to the nearest 100ms
  * The price depends on the amount of memory you allocate to your function
  * Charged $0.00001667 for every GB-second used



Why Lambda?

* There are no servers that need to be managed, and hence no administrators for various roles
* No worry on scalling, it just scales automatically, it does not fall over unless there is a rare outage 
* It is incredibly cheap



##### Exam tips

* Lambda scales out (not up) automatically
  * millions of functions running in parallel at the same time. However there is potential for running out of memory
* Functions are independent, 1 event = 1 function
* Lambda is serverless (also it is a compute service)
* Know what services are serverless: API Gateway, S3, DynamoDB
  * RDS, Beanstalk, EC2 not serverless
* Lambda functions can trigger other lambda functions, i.e. 1 event can = x functions if there are triggers on other functions
* AWS X-Ray can help debug what is happening
* Can do things globally, it is not stuck in one specific region. i.e. you can back up S3 buckets into other S3 buckets
* Know your triggers***



### API Gateway

An API is and Aplication Programming Interface

* As an example think about a restaurant. The couple does not want to chat to the chef and the chef s=is busy so does not want to come out. Hence the waiter. 

* The waiter takes the order and passes it to the chef, the chef processses the order and does what is necessary before returning the meal, which the waiter then returns to the couple

* Essentially, the waiter is the API

* Types of APIs

  * REST APIs (REpresentation State Transfer)
    * Uses JSON
  * SOAP APIs (Simple Object Access Protocol)
    * Wasn't actually all that simple, makes use of XML

* Amazon API Gateway

  * is a fully managed service that makes it easy fir developers to publish, maintain, monitor, and secure APIs at anys cale. With a few clicks in the AWS Management Console, you can create an API that acts as a "front door" for applications to access data, business logic, or functionality from your back-end services, such as applicatios running on Amazon EC2, code running on AWS Lambda, or any web application 
  * API Gateway is our waiter, it can forward requests to various places
  * It exposes HTTPS endpoints to define a RESTful API (i.e. an address to which we can make calls to)

* What can it do?

  * It allows you to server less-ly connect to services like Lambda and DynamoDB
  * Send each API endpoint to a different target
  * Run efficiently with low cost
  * Scales effortlessly
  * Able to track and control usage by API key
  * Able to throttle requests to prevent attacks
  * Can connect to CloudWatch to log all requests for monitoring
  * can maintain multiple versions of your API

  * How do you configure it?
    * Define an API (container)
    * Define resources and nested resources (URL paths)
    * For each resources:
      * Select supported HTTP methods (verbs)
      * Set security
      * Choose target 
      * Set request and response transformations
    * Can deploy the API to a stage
      * Uses AP gateway domain, by default but you can use a custom domain
      * NOw supports AWS Certificate Manager 

* What is API caching?

  * You can enable API caching in Amazon API gateway to cache your endpoints response. With caching, you can reduce the number of calls made to your endpoint and also improve the latency of the requests to your API. When you enable caching for a stage, API Gateway caches responses from your endpoint for a specified TTL period, in seconds. API Gateway then responds to the request by looking up the endpoint response from the cache instead of making a request to your endpoint

* Same Origin Policy

  * In computing, the same-orignin policy is an important concept in the web application security model. Under the policy, a web browser permits scripts contained in a first web page to access data in a second web page, but only if both web pages have the same origin.
  * This is done to prevent Cross-Site scripting (XSS) attacks
  * It is enforced by web browsers
  * it is ignored by tools like Postman and curl

* Cross-Origin Resource Sharing (CORS)

  * is one way that the server at the other end (not the client code in the browser) can relax the same-origin policy
  * CORS is a mechanism that allows restricted resources on a web page to be requested from another domain outside the domain from which the first resource was served
  * Essentially what happens:
    * A browser makes an HTTP OPTIONS call for a URL
      * OPTIONS is an HTTP method
    * Server returns a response that says: "These other domains are approved to GET this URL"
    * Error - "Origin policy cannot be read at the remote source?" so you need to enable CORS on the API gateway



##### Exam Tips

* Remember what API gateway is at a high level
  * i.e. the waiter that is going to take your order
* API Gateway has caching capabilities to increase performance
* It is low cost and scales automatically
* You can throttle API Gateway to prevent attacks (Amazon do that automatically anyway)
* You can log results to CLoudWatch
* If you are using Javascript/AJAX that uses multiple domains with API Gateway, ensure that you have enable CORS 
* CORS is enforced by the client



### Version Control with Lambda

When you use version in AWS Lambda, you can publish one or more versions of your Lambda function. As a result, you can work with different variations of your Lambda function in your development workflow, such as development, beta and production versions

Each Lambda function version has a unique ARN. After you publish a version, it is immutable 	



Qualified/Unqualified ARNs

* You can refer to this function using its ARN. There are 2 ARNs associated with this initial version
  * Qualified ARN - the function ARN with the version suffix
    * i.e. arn:aws:lambda:aws-region:acct-id:function:helloworld:$LATEST
  * Unqualified ARN - the function ARN without the version suffix
    * i.e. arn:aws:lambda:aws-region:acct-id:function:helloworld



Alias

* After initially creating a Lambda function (the $LATEST version), you can publish a version 1 of it
* By creating an alias named PROD that points to version 1, you can now use the PROD alias to invoke version 1 of the Lambda function
* Now you can update the code (the $LATEST version) with all of your improvements, and then publish another stable improved version (version 2)
* You can promote version 2to production by remapping the PROD alias so that it points to version 2
* If you find something wrong, you can easily roll back the production version to version 1 by remapping the PROD alias so that it points to version 1 



###### Exam Tips

* Can have multiple versions of lambda functions
* Latest version will use $LATEST
* Qualified version will use arn+$LATEST, unqualified will not have it (i.e. it will just be the arn)
* Versions are immutable (cannot be changed)
* Can split traffic using aliases to different versions
  * cannot split traffic with $LATEST, instead create an alias to latest

### Polly to help with Exam

Create a skill on Alexa site - it's epic!

### Step Functions

Step functions allow you to visualise and test your server less applications

* they provide a graphical console to arrange and visualise the components of your application as a multistep series of steps
* this makes it simple to build and run multistep applications
* step functions automatically triggers and tracks each step, and retries when there are error, so your application executes in order and as expected
* step functions logs the state of each step, so when things do go wrong, you can diagnose and debug problems quickly



Create them using JSON Amazon states language



### X-Ray

X-Ray is a service that collects data about requests that your application serves, and provides tools you can use to view, filter, and gain insights into that data to identify issues and opportunities for optimization

* For any traced request to your application, you can see detailed information not only about the request and the response, but also about the calls that your application makes to downstream AWS resources, micro services, databases and HTTP web APIs
* Way of visualising your serverless application



How it works

* Your application, within the X-ray SDK, will send messages to the X-Ray Daemon
* Daemon listens on UDP, takes the data and sends it to the X-Ray API 
* API stores all of this data and then creates a sort of visualisation of it all in the X-Ray Console
* We can also have our normal scripts and tools that communicate with the API or Daemon directly



The X-Ray SDk provides:

* Inetrceptors to add to your code to trace incoming HTTP requests
* Client handlers to instrument AWS SDK clients that your application uses to call other AWS services
* An HTTP client to use to instrument calls to other internal and external HTTP web services 

The X-Ray integrates with the following AWS services:

* Elastic Load Balancing
* AWS Lambda
* Amazon API Gateway
* Amazon Elastic Cloud Compute (EC2)
* AWS Elastic Beanstalk 

Supported languages:

* Java
* Go
* Node.js
* Python
* Ruby
* .Net



### Advanced API Gateway

You can use the **API Gateway Import** API feature t import an API from an external definition file into API Gateway

* Currently, the Import API feature supports Swagger v2.0 definition files

With the Import API, you can either create a new API by submitting a POST request that includes a Swagger definition in the payload and endpoint configuration, or you can update an existing API by using a PUT request that contains a Swagger definition in the payload

* You can update an API by overwriting it with a new definition, or merge a definition with an existing API
* You specify the options using a mode query parameter in the request URL



API Throttling

* By default, API Gateway limits the steady-state request rate to 10,000 requests per second (rps)
* The maximum concurrent requests is 5000 request across all APIs within an AWS account
* If you go over 10,000 requests per second or 5000 concurrent requests you will receive a **429 Too Many Request** error response 
* Example
  * If a caller submits 10,000 requests in a one second period evenly (for example 10 requests every millisecond), API Gateway processes all requests without dropping any
  * If the caller sends 10,000 requests in the first millisecond, API Gateway serves 5,000 of those requests and throttles the rest in the one-second period
  * If the caller submits 5,000 requests in the first millisecond and then evenly spreads another 5000 requests through the remaining 999 milliseconds (for example, about 5 requests every millisecond), API Gateway processes all 10,000 requests in the one-second period without return 429 error responses

SOAP Webservice Passthrough

* You can configure API Gateway as a SOAP web service passthrough 



### Serverless Summary

See exam tips above!



# 6. Dynamo DB

### Intro to Dynamo DB

What is DynamoDB?

* It is a fast and flexible NoSQL database service for all applications that need consistent, single-digit millisecond latency at any scale. 
* It is a fully managed database and supports both documents and key-value data models.
* Its flexible data model (i.e. no need to define database schema upfront) and reliable performance make it a great fit for mobile, web, gaming, ad-tech, IoT and many other applications 

Stored on SSD storage

It is spread across 3 geographically distinct data centres (avoids single point of failures)

You have a choice of 2 consistency models:

1. Eventual consistent reads (default)
   * Consistency across all copies of data is usually reached within a second. Repeating a read after a short time should return the updated data (best read performance)
2. Strongly consistent reads
   * A strongly consistent read returns a result that reflects all writes that received a successful response prior to the read

DynamoDB is made up of:

* Tables
* Items (think a row of data in a table)
* Attributes (think of a column of data in a table)
* Supports key-value and document data structures
* Key = the name of the data, Value = the data itself
* Documents can be written in JSON, HTML or XML



Dynamo DB stores and retrieves data based on primary keys

* 2 types of primary key:
  * **Partition Key**
    * is a unique attribute, like userID
    * value of the partition key is input to an internal hash function which determines the partition or physical location on which the data is stroed
    * if you are using this key as your primary key, then no two items can ever have the same partition key
  * **Composite Key** (Partition Key + Sort Key) in combination
    * Usually used when the partition key itself isn't always unique
    * Example is someone posting multiple times to a forum
    * primary key would be a composite key consisting of the partition key (userID) and the sort key (a timestamp of the post)
    * 2 items may have the same partition key but they must then have a different sort key
    * all items with the same partition key are stored together, and then sorted according to the sort key value
    * this allows you to store multiple items with the same partition key



DynamoDB Access Control

* authentication and access control to DynamoDB is managed via AWS IAM
* you can create an IAM user within your AWS account which has specific permissions to access and create DynamoDB tables
* you can create an IAM role which enables you to obtain temporary access keys that can be sued to access AWS services like DynamoDB
* you can also use a special IAM Condition to restrict user access to only their records
  * i.e. view data where the partition key value matches their userID, through the policy document



### General Exam Tips

* Amazon DynamoDB is a low latency NOSql database
* It consists of Tables, Items and Attributes
* Supports both document and key-value data models
* Supported document formats are JSON, HTML and XML
* 2 types of primary key: Partition Key and a combination of Partition Key + Sort Key (Composite Key)
* 2 consistency models: Strongly consistent / Eventually consistent
* Access is controlled using IAM polocies
* Fine grained access control using IAM Condition parameter: **dynamodb:LeadingKeys** to allow users to access only the items where the partition key value matches their userID



### Creating a DynamoDB table Lab

* You can create a bootstrap script for your EC2 instance
* Not every items needs to have all of the attributes
* EC2 instances can only interact with the dynamo DB table if they have the correct IAM roles and permissions



### Indexes Deep Dive

What is an index?

* In SQL databases, an index is a data structure which allows you to perform fast queries on specific columns in a table. You select the columns that you want included in the index and run your searches on the index - rather than on the entire dataset
* DynamoDB is not a SQL DB but still has this feature
* In DynamoDB, 2 types of Index are supported ti help speed-up your DynamoDB queries:
  * Local Secondary Index
    * can only be created when you are creating the table
    * you cannot add, remove, or modify it later
    * it has the same partition key as your original table but a different sort key
    * it gives you a different view of your data, organised according to an alternative sort key
    * any queries based on this sort key are much faster using the index than the main table
  * Global Secondary Index
    * you can create when you create your table, or add it later
    * allows for a different partition key as well as a different sort key
    * it gives you a completely different view of the data
    * speeds up any queries relating to this alternative partition and sort key

##### Indexes Exam Tips

* Indexes enable fast queries on specific data columns
* Gives you a different view of your data, based on alternative partition / sort keys
* Important to understand the differences between local and global secondary indexes



### Scan vs Query API Calls

*What is a Query?*

* a query operation finds items in a table based on the primary key attribute and a distinct value to search for
  * e.g. select an item where the userID is equal to 212, will select all of the attributes for that item, e.g. first name, surname, email, etc.
* Use an optional sort key name and value to refine the results
  * e.g. if your sort key is a timestamp, you can refine the query to only select items with a timestamp of the last 7 days
* By default, a Query returns all of the attributes for the items but you can use the **ProjectionExpression** parameter if you want the query to only return the specific attributes you want
  * e.g. if you only want to see the email address rather than all of the attributes 
* Results are always sorted by the sort key
* If the results are numeric or ASCII, by default sorted ascending
* You can reverse the order by setting the **ScanIndexForward** (relates to queries only NB) parameter to false
* By default, Queries are eventually consistent
* You need to explicitly set the query to be strongly consistent 



*What is a Scan?*

* A scan operation examines every item in the table
* By default, it returns all data attributes
* Use the **ProjectionExpression** parameter to refine the scan to only return the attributes you want
* So it scans the entire DB and then dumps a filter on top of that



*Query or Scan?*

* Query is more efficient than a scan
* Scan dumps the entire table, then filters out the values to provide the desired result - removing the unwanted data
  * This adds an extra step of removing the data you do not want
* As the table grows, the scan operation takes longer
* Scan operation on a large table can use up the provisioned throughout for a large table in just a single operation



*How to Improve Performance*

* You can reduce the impact of a query or a scan by setting a smaller page size which uses fewer read operations
  * e.g. set the page size to return 40 items
* Larger number of smaller operations will allow other requests to succeed without throttling
* Avoid using scan operations if you can; design tables in a way that you can use the Query, Get, or BatchGetItem APIs



*How to Improve Scan Performance*

* By default, a scan operation processes data sequentially in returning 1MB increments before moving on to retrieve the next 1MB of data. It can only scan one partition at a time
* You can configure DynamoDB to use Parallel scans instead by logically dividing a table or index into segments and scanning each segment
* Best to avoid parallel scans if your table or index is already incurring heavy read / write activity from other application



###### Scan vs Query Exam Tips

* A query operation finds items in a table using only the primary key attribute
  * You provide the primary key name and a distinct value to search for
* A scan operation examines every item in the table
  * By default, it returns all data attributes
* Use the **ProjectionExpression** parameter to refine the results
* Query results are always sorted by the sort key if there is one
  * sorted in ascending order
  * Set ScanIndexForward parameter to false to reverse the order - remember this applies to queries only
* Query  operations are generally more efficient that a scan
* Reduce the impact if a query or scan by setting a smaller page size which uses fewer read operations
* Isolate scan operations to specific tables and segregate them from your mission-critical traffic
* Try parallel scans, rather then the deafult sequential scan
* Avoid using scan operations if you can: design tables in a way that you can use the Query, GetItem and BatchGetItem APIs



### Provisioned Throughput

Mechanism used to describe the capacity and performance requirements in DynamoDB

Read and Write Capacity Units

* DynamoDB provisioned throughout is measured in capacity units
* When you create your table, you specify your requirements in terms of Read Capacity Units and Write Capacity Unites
* 1x Write Capacity Unit = 1x 1KB Write per second
* 1x Read Capacity Unit = 
  * 1x Strongly Consistent Read of 4KB per second **OR**
  * 2x Eventually Consistent Reads of 4KB per second (Default)
* So a table with 5x read capacity units and 5x write capacity units will be able to perform:
  * 5x 4KB strongly consistent reads = 20KB per second
  * Twice as many eventually consistent = 40KB
  * 5x 1KB writes = 5KB per second
  * If your application reads or writes larger items it will consume more capacity units and all cost you more as well



Working out what capacity you will need for your application:

* Say that your application needs to read 80 items (table rows) per second. Each item is 3KB in size and you need strongly consistent reads
* First, calculate how many read capacity units needed for each read:
  * size of each item / 4KB i.e. 3KB / 4KB = 0.75
  * rounded-up to the nearest whole, each read will need 1x read capacity unit per read operation
  * multiplied by the number of reads per second = 80 read capacity units required
* The same calculation can be made for eventually consistent reads, but since you can do 2x 4KB reads per second, you can divide the final answer by 2 = 40 read capacity units required for eventually consistent reads 



Now lets calculate write capacity units:

* Say that you want to write 100 items per second, with each item being 512 bytes in size
* First, calculate how many capacity units required for each write:
  * Take the size of each item and divide by 1KB, i.e. 512 bytes / 1KB = 0.5
  * Rounded up to the nearest whole, you will need 1x write capacity units per write operation
  * Multiply this by the number of writes per second = 100 write capacity units required



###### Exam Tips

* Provisioned throughput is measured in capacity units
* 1x write capacity unit =  1x 1KB write per second
* 1x read capacity unit = 1x 4KB strongly consistent read OR 2x 4KB eventually consistent reads per second 
* Remember how to do the calculation!
  * Divide to get units per write, round up and multiply by the number of required write items. The calculation for read and write is the same (**aside from the divide value really**)



### Dynamo DB Accelerator (DAX)

What is DAX?

* A fully managed, clustered and in-memory cache for DynamoDB
* Performance boost for read only!
* Delivers up to a 10x read performance improvement
* Microsecond performance for millions of request per second 
* Ideal for read-heavy and bursty workloads e.g. auction applications, gaming and retail sites during black Friday promotions



How does it work?

* DAX is a write-through caching service (this means that data is written to the cache as well as the back end store at the same time)
* Allows you to point your DynamoDB API calls at the DAX cluster (Instead of querying the DB, queries the DAX first)
* If the item you are querying is in the cache (cache hit), DAX returns the result to the application
* If the item is not available (cache miss) then DAX performs an eventually consistent GetItem operation against DynamoDB
* Retrieval of data from DAX reduces the read load on your DynamoDB tables, and in some cases, may be able to reduce the provisioned read capacity on your tables (i.e. money saved)



What is Dax not suitable for?

* Caters for eventually consistent reads only - so it is not suitable for applications that require strongly consistent reads
* Write intensive applciations
* Applications that do not perform many read operations
* Applications that do not require microsecond response times (i.e. low latency)

###### Exam Tipes

* Provides in-memory cache for DynamoDB tables
* Improves response times for eventually consistent reads only
* You point your API calls to the DAX cluster rather than to your table
* If the item queried is in the DAX, it will return it, else it will perform an eventually consistent GetItem operation to your DynamoDB table
* It is not suitable for write-intensive applications or applications that require strongly consistent reads



### Elasticache

In memory cache that can sit informant of any RDS DB as well as DynamoDB



What is Elasticache?

* it is in-memory cache in the cloud
* it improves performance of web applications, allowing you to retrieve information from fast in-memory caches rather than slower disk based dbs
* sits between your application and the db
* takes the load off of your db
* it is good if your db is particularly read-heavy and the data is not changing frequently



Benfits

* improves performance for read-heavy workloads
  * i.e.e social networking, gaming and media sharing
* frequently accessed data is stored in memory for low-latency access, improving the overall performance of your applications
* also good for heavy workloads (e.g. recommendation engine)
* can be used to store results if I.O intensive db queries or output of compute-intensive applications



There are 2 types of Elasticache

* **Memcached**
  * Widely adopted memory object caching system
  * Multi-threaded
  * No multi-AZ capability (i.e. not clustered, standalone)
* **Redis**
  * Open-source in-memory key-value store
  * Supports more complex data structures: sorted sets and lists
  * Supports Master / Slave replication and multi-AZ for cross-AZ redundancy 

There are 2 Caching Strategies available 

* **Lazy loading**
  * loads the data into the cache only when necessary
  * If requested data is in the cache, Elasticache returns the data to the application
  * If the data is not in the cache or has expired Elasticache returns null
    * Your application the fetches the data from the db and writes the data received to the cache so that it is available next time
  * **Advantages**
    * only the requested data is cached: avoids filling up the cache with useless data
    * node failures are not fatal, a new empty node will just have a lot of cache misses initially
  * **Disadvantages**
    * cache miss penalty: initial request means a query to the db, writing of the data to the cache (i.e. extra overhead)
    * stale data - if the data is only updated when there is a cache miss, it can become stale. Doesn't automatically update if data in the db changes
    * Dealing with stale data; addition of a TTL (no. of seconds until expiry). Expired data is treated as a cache miss. Does not eliminate stale data, but does help to avoid it.
* **Write through**
  * Adds or updates data to the cache whenever data is written to the database
  * **Advantages**
    * data in the cache is never stale
    * users are generally more tolerant of additional latency when updating data than when retrieving it
  * **Disadvantages**
    * write penalty: every write involves a write to the cache as well as a write to the db
    * if a node fails, and a new one is spun up, data is missing until data is written to (add/updated) the db (which can be mitigated by implementing lazy loading in conjunction with write-through)
    * Land up with wasted resources if most of the data in your DB is never read



###### Exam Tips

* In memory cache that sits between you application and the db
* 2 different caching strategies: lazy loading and write-through
* Lazy loading only caches the data when requested (remember the advantages and disadvantages)
* Write-through writes to the cache whenever there is a write to the db (remember the advantages and disadvantages )



### DynamoDB Summary

* DynamoDB is a low-latency NoSQL DB
* Consists of tables Items and Attributes
* Supports both document and key-value data models
* Supported document formats are JSON, HTML and XML
* 2 types of primary keys: Partition key and the combo of Partition Key and Sort Key (Composite key)
* 2 consistency models: Strongly consistent and Eventually consistent (default)
* Access is controlled uniting IAM policies
  * dynamodb:LeadingKeys fine grained access parameter (access only the items where the partition key value matches their user ID)
* Indexes enable fast queries on specific data columns
* Important to understand the differences between local secondary index and global secondary index
* Query vs Scan
  * Query results are always sorted by the sort key if there is one
  * ScanIndexForward is only for queries!
  * Improve performance by using a smaller page size, parallel scans or isolate operations to specific tables
* Provisioned throughout is measure in capacity units
  * remember how you would do the calculations
* DAX provides in-memory caching for DynamoDB tables
  * improves performance for eventually consistent reads only
* Elasticache is an in memory cache that sites between your application and the db
  * lazy loading vs write-through strategies





# 7. AWS Key Management

AWS Key Management Services (AWS KMS) is a managed service that makes it easy for you to create and control the encryption keys used to encrypt your data

AWS KMS is integrated with other AWS services including EBS, S3, Amazon Redshift, Amazon Elastic Transcoder, Amazon WorkMail, Amazon RDS, and others to make it simple to encrypt you data with encryption keys that you manage 

Good practice to have one user that controls keys and the other to do the encryption/decryption - that way you get two levels of security

Encryption keys are regional



Key material options:

* Use KMS generated key material
* Import your own key material 

**The Customer Master Key:**

* CMK consists of 
  * alias
  * creation date
  * description
  * key state
  * **key material (either customer provided or AWS provided)**
* Can NEVER be exported (need to use cloudHSM if need to export)



### KMS API Calls

* aws kms encrypt --keyid <id> --plaintext fileb://<location --output text --query CiphertextBlob | base64 --decode > encryptedsecret.txt
* aws kms decrypt --ciphertext-blob fileb:<location> --output text --query Plaintext | base64 --decode > decryptedsecret.txt
* aws kms re-encrypt --destination-key-id <id> --ciphertext-blob fileb://<location | base64 > newencryption.txt
* aws kms enable-key-rotation --key-id <id> (rotates the key each year)



Actual API calls:

* aws kms encrypt
* aws kms decrypt
* aws kms re-encrypt (re-encrypts an encrypted one)
* aws kms enable-key-rotation



### KMS Envelope Encryption

master key --- <encrypts> --> Envelope Key (Data Key) ---<encrypts> --> Data



It is the process of encrypting the envelope key (which is used to encrypt the data)

Encryption of the key that encrypts the data

To unencrypted, we use the master algorithm in an algorithm for the plain text key



Tips:

* The customer master key is used to decrypt the data key (envelope key)
* Envelope key is used to decrypt the data



### KMS Exam Tips

* When you delete your key - you have to disable it first and then schedule that deletion before it actually gets deleted (min 1 week wait)
* Remember what KMS is: managed service that makes it easy for you to create and control encryption keys used to encrypt your data
* Create a customer master key - CMK which can never be exported (multi-tenanted hardware)
* Admin and usage permissions for keys for different users is good practice
* Remember the API calls
* Do not use re-encrypt for key-rotation
* CMK decrypts the data key which is the key used to decrypt the data





# 7. Other AWS Services

## 7.1. SQS

* Simple queue service
* Actually the first ever AWS service available to the public
* What is it?
  * It is a web service that gives you access to a message queue that can be used to store messages while waiting for a computer to process them
  * Amazon SWS is a distributed queue system that enables web service applications to quickly and reliably queue messages that one component in the application generates to be consumed by another component
  * A c queue is a temporary repo for messages that are awaiting processing 
* It is a pull-based system always (SNS is a push-based system)
* Messages remain in the queue even if the applications servers serving the queue or pulling from it fail
* Using SQS, you can decouple the components of an application so they run independently, easing message management between components
* Any component of a distributed application can store messages in the queue
* Messages can contain up to 256kb of text in any format
* Any component can later retrieve the messages programatically using the SQS API  
* The queue acts as a buffer between the component producing and saving data, and the component receiving the data for processing
  * This means that the queue resolves issues that arise if the producer is producing work faster than the consumer can process it, or if the producer or consumer are only intermittently connected to the network
* Able to build autoscaling into this, allowing for elasticity 
* Queue types
  * Standard Queue (default)
    * Lets you have nearly unlimited number of transactions per second
    * Standard queues guarantee that a message is delivered at least once
    * Occasionally (because of the highly-distributed architecture that allows high throughput), more than one copy of the message might be delivered out of order
    * These provide best-effort ordering which ensures that message are generally delivered in the same order as they are sent (no real ordering)
  * FIFO Queue 
    * Complements the standard queue
    * Most important features of FIFO queues are the FIFIO delivery and exactly-once processing: the order in which messages are sent and received is strictly preserved and a message is delivered once and remains available until a consumer processes and deletes it
    * Duplicate sare not introduced into the queue
    * Support message groups that allow multiple ordered message groups within a single queue
    * Limited to 300 transactions per second (TPS), but the queues have all the capabilities of standard queues
  * SQS Key facts
    * Pull-based and not push-based
    * Messages are 256kb in size
    * Messages can be kept in the queue from 1min to 14days
    * Default retention period is 4 days
    * SQS guarantees that you message will be processed at least once (might be more depending on timeout)
  * Visibility Timeout
    * This is the amount of time that the message is invisible in the SQS queue after a reader picks up that message
    * Provided the job is processed before the visibility timeout expires, the message will then be deleted from the queue
    * If the job is not processed within that time, the message will become invisible again and another reader will process it
    * This could result in the message being delivered twice
    * Default timeout is 30s which should be increased if your task takes longer than 30s
    * Max time is 12hrs
  * Long polling saves you money because it only polls if a message comes in or there is a timeout
    * Saves you money because your not polling when something may not be there
    * Max long polling timeout is 20s



###### Exam Tips

* SQS is a distributed message queuing system
* Allows you to decouple components of an application so that they are independent
* Pull-based, not push-based
* Standard queues are the default - best-effort ordering; messages delivered at least once
* FIFO queues - ordering is strictly preserved, messages delivered once, no duplicated (i.e. good for banking transactions which need to happen in strict order)
* Visibility timeout default is 30s which should be increased if your task takes longer than 30s to complete (max is 12hrs)
* Short polling return immediately even if no messages are in the queue
* Long polling polls the queue periodically and only returns a response when a message is in the queue or when the timeout period is reached





## 7.2. SNS - Simple Notification Service

What is SNS?

* It is a web service that makes it easy to setup, operate, ad send notifications from the cloud
* It provides developers with a highly scalable, flexible, cost-effective capability to publish messages from an application and immediately deliver them to subscribers or other applications
* Push notifications to Apple, Google, Fire OS and Windows devices, as well as Android devices in China
* Besides pushing cloud notifications directly to mobile devices, SNS can also deliver notifications by SMS text message or email to SQS queues, or to any HTTP endpoint 
* SNS notifications can also trigger lambda functions
  * i.e. when a message is published to an SNS topic that has a Lambda functions subscribed to it, the Lambda function is invoked with the payload of the published message
  * The lambda function receives the message payload as an input parameter and can manipulate the information in the message, publish the message to another SNS topic or send the message to other AWS services 



SNS Topics

* SNS allows you to group multiple recipients using topics. A topic is an access point for allowing recipients to dynamically subscribe for identical copies of the same notification
* One topic can support deliveries to multiple endpoint types 
  * i.e. you can group together iOS, Android and SMS recipients
  * When you publish once to a topic, SNS delivers appropriately formatted copies of your message yo each subscriber
* To prevent messages from being lost, all messages published to SNS are stored redundantly across multiple AZs



SNS Benefits

* Instant, push-based delivery (no-polling)
* Simple APIs and easy integration with applications
* Flexible message delivery over multiple transport protocols
* Inexpensive, pay-as-you-go model with not up-front costs
* Web-based management console offers the simplicity of a point-and-click interface



SNS vs SQS

* Poll vs push



Pricing

* Users pay $0.50 per 1 million SNS requests
* $0.06 per 100,000 notification deliveries over HTTP
* $0.75 per 100 notification deliveries over SMS
* $2 per 100,000 notification deliveries over email 



SNS follows the "publish-subscribe" (pub-sub) messaging paradigm, with notifications being delivered to clients using a "push" mechanism that eliminates the need to periodically check or "poll" for new information and updates



With simple APIs requiring minimal up-front development effort, no maintenance or management overhead and pay-as-you-go pricing, SNS gives developers an easy mechanism to incorporate a powerful notification system with their applications



###### Exam Tips

* SNS is a scalable and highly available notification service which allows you to send push notifications from the cloud
* Variety of message formats are supported: SMS text message, email, SQS queues, any HTTP endpoint
* Pub-sub model whereby users subscribe to topics
* It is a push mechanism rather than a pull (poll) mechanism 



## 7.3. SES vs SNS - Simple Email Service

What is SES?

* It is a scalable and highly available email service designed to help marketing teams and application developers to send marketing, notification, and transactional emails to their customers using a pay-as-you-go model
* Cal also be used to receive emails; incoming mails can be deliver automatically to an S3 bucket
* Incoming mails can be used to rigger Lambda functions and SNS notifications
* It is email only (best way to think is marketing emails)



Use cases

* Automated mails
* Purchase confirmations, shipping notifications, order status updates
* Marketing communications, advertisements, newsletters and special offers
  * e.e. an online retail business that needs to let customers know about sales promotions and discounts



###### Exam Tips and Comparisons 

* SES is email only, SNS is pub/sub messaging service with formats that include SMS, HTTP, SQS, email
* SES can trigger lambda functions or SNS notifications, SNS can also trigger lambdas
* SES can be used for both incoming and outgoing email, SNS can fan out messages to large number or recipients (replicate and push messages to multiple endpoints and formats)
* SES requires just an email address to start sending messages to a user, SNS consumers must subscribe to a topic to receive the notification 



## 7.3. ElasticBeanstalk 101

What is it?

* It is a service for deploying and scaling web applications developed in many popular languages: Java, .NET, PHP, Node.js, Python, Ruby, Go, and Docker onto widely used application server platforms like Apache Tomcat, Nginx, Passenger and IIS
* Developers can focus on writing code and don't need to worry about any of the underlying infrastructure needed to run the application 
* i.e. You through it some source code in a zipped file, and it will go in and configure an environment for you (web servers, RDS, etc.)
* It is really a provisioning service, similar to CloudFormation 
* Elastic Beanstalk automatically manages deployment, capacity provisioning, load balancing, auto-scaling and application health
* You retain full control of your underlying AWS resources powering your application and you pay only for the AWS resources required to store and run your applications (e.g. EC2 instances and S3 buckets)



Additionally;

* It is the fastest and simplest way to employ your applications in AWS
* Automatically scales your application up and down
* You can select the EC2 instance type that it optimal for your application
* You can retain full admin control over the resources powering your application, or have Elastic Beanstalk do it for you
* Managed Platform Updates feature automatically applies updates to your OS, Java, PHP, Node.js etc.
* Monitor and manage application health via a dashboard
* Integrated with CloudWatch and X-Ray for performance data and metrics



###### Exam Tips

* It is a way of deploying your application with all of the necessary resources being provisioned automatically for you
* It deploys and scales your web application including the web application server platform when required
* Supports widely used programming technologies - Java, PHP, Python, Ruby, Go, Docker, .NET, Node.js
* And application server platforms like Tomcat, Passenger, Puma and IIS
* Provisions the underlying resources for you
* Can fully manage the EC2 instances for you or you can take full administrative control
* Updates, monitoring, metrics and health checks are all included 

CloudFormation is a way of scripting your infrastructure, while Elastic Beanstalk is a way of provisioning your resources by means of a GUI



### Updating Elastic Beanstalk

What is an EBS Deployment policy?

* Elastic Beanstalk supports several options for processing deployments:
  * All at once
    * Deploys the new version to all instances simultaneously 
    * Al of your instances are out of service while the deployment takes place
    * You will experience an outage while the deployment is taking place - **not ideal for mission-critical production systems** 
    * If the update fails, you need to roll back the changes by re-deploying the original version to all your instances
  * Rolling
    * Deploys the new version in batches
    * Each batch of instances is taken out of service while the deployment takes place
    * Your environment capacity will be reduced by the number of instances in a batch while the deployment takes place
    * **Not ideal for performance sensitive systems**
    * If the update fails, you need to perform additional rolling update to roll back the changes
  * Rolling with additional batch
    * Launches an additional batch of instances
    * Deploys the new version in batches
    * **Maintains full capacity during the deployment process**
    * If the update fails, you need to perform an additional rolling update to roll back the changes
  * Immutable 
    * Deploys the new version to a fresh group of instances in their own new autoscaling group
    * When the new instances pass their health checks they are moved to your existing autoscaling group; and finally, the old instances are terminated
    * Maintains full capacity during the deployment process
    * The impact of a failed update is far less, and the rollback process requires only terminating the new autoscaling group
    * Preferred option for mission critical systems



If you just have one instance, you are going to get options for All at once and Immutable only because you do not have a fleet of instances



###### Exam Tips

Remember the differences between the policies!



### Advanced Elastic Beansatlk

Configuring Elastic Beanstalk

* You can customise your Elastic Beanstalk environment using Elastic Beanstalk configuration files (e.g. you can define packages to install, create Linux users and groups, run shell commands, specify services to enable or configure your load balancer etc.)
* These files are written in YAML or JSON format
* They can have have a filename of your choice but must have a .config extension and be saved inside a file called .ebextensions
  * which must be included in the top-level directory of your application source code bundle
  * This means that the config files can be placed under source control along with the rest of your application code

###### Exam Tips

* Customisation can be added to an Elastic Beanstalk environment by adding configuration files
* Files are written in YAML or JSON
* Files have a .config extension 
* The .config files are saved to the .ebextensions folder
* Your .ebextensions folder must be located at the top level directory of your application source code bundle



### RDS and Elastic Beanstalk

* EBS supports two ways of integrating an RDS database with your Beanstalk environment

  You can launch the RDS instance from **within the Elastic Beanstalk** console, which means that the RDS instance is created within your Elastic Beanstalk environment - a good option for Dev and Test deployments

  * However, this may not be ideal for prod environments because it means the lifecycle of your database is tied to the lifecycle of your application environment - if you terminate the environment, the database instance will be terminated too
  * Coupling means that when you delete the beanstalk env, you delete the RDS env too
  * However, this is a quick and easy way to add your db and get started 

* For prod environments, the preferred option is to decouple the RDS instance from your EBS env; i.e. launch it **outside of Elastic Beanstalk**, directly from the RDS section of the console

  * This option gives you a lot more flexibility, allows you to connect multiple environments to the same database, provides a wider choice of db types, and allows you to tear down your application environment without affecting the database instance
  * To allow the EC2 instances in your Elastic Beanstalk environment to connect to an outside database, there are two additional configuration steps required:
    * An additional security group must be added to your environment's auto scaling group
    * You will need to provide connection string configuration information to your application servers (endpoint, password, username and conn params usingElastic Beanstalk environment properties)



## 7.4. Kinesis 101

What is streaming data?

* It is data that is generated continuously by thousands of data sources, which typically send in the data records simultaneously and in small sizes (order of kilobytes)
* things like purchases from online stores, stock prices, game data as someone is playing, social network data, geospatial data (think uber.com), IoT sensor data



What is Kinesis?

* Kinesis is a platform on AWS to send your streaming data too. Kinesis makes it easy to load and analyse streaming data, and also providing the ability for you to build your own custom application for your business needs
* Core services:
  * Kinesis Streams
    * Things like EC2, mobile phones, laptops and IoT. Also get Video Streams vs Data Streams 
    * These are producers that send data to Kinesis streams 
    * Default is storage for 24hrs but can also be up to 7 day retention
    * Stored in shards (give you 5 transactions per second for reads, up to a max total data read rate of 2MB per second and up to 1000 records per second for writes, up to a max total data write rate of 1mb per second including partition keys). The data capacity of your stream is a function of the number of shards that you specify for the system. The total capacity of the stream is the sum of the capacities of its shards
    * Consumers the take data from the shard before doing something useful to then be stored elsewhere
  * Kinesis Firehose
    * Allows you to capture, transform, load data streams into AWS data stores for near real-time analytics with BI tools
    * Similar in that we have a series of producers that feed the firehose
    * No worry about shards, streams, etc - it is all automated
    * No data retention window, as it comes in it is either analysed or sent directly to where it is configured to be sent to
  * Kinesis Analytics
    * Allows you to run SQL queries as it comes through the firehose or streams 

###### Exam Tips

* remember the differences between streams and firehose, with its architectural differences
* Also remember a high level understanding of what Kinesis Analytics is.





# 8. Developer Theory

### CI/CD

What is CI/CD?

* CI and CD are best practices for software development and deployment
* They enable frequent software changes to be applied whilst maintaining systems and service stability
* Companies like AWS, Netflix, Google and FB have pioneered this approach to releasing code, successfully applying thousands of changes per day



CI Workflow

* Multiple developers working on features or bug features, all sharing the same repository and all contributing to the same application, frequently pushing their updates to the repo
* The code repo would be integrated with a build management system where code changes trigger an automated build
* However, we need a way to ensure that any code changes does not break the build or introduce new bugs into the application
  * The test system runs automated tests on the newly built application, identifies any bugs, which prevents any issues from being introduced into the master code
* CI focuses on small code changes which frequently are committed to the main repository once they have been successfully tested 



Continuous Delivery / Continuous Deployment

* CD can mean either of the two above
* Continuous Delivery is a development practice where merged changes are automatically built, tested, and prepared for release into staging and eventually production environments
  * There is usually a manual decision process to initiate deployment of the new code
* Continuous Deployment  takes the idea of automation one step further and automatically deploys the new code following successful testing, eliminating any manual steps
  * The new code is automatically released as soon as it passes through the stages of your release process (build, test, package)
  * Small changes are released early and frequently
* Both practices require the build, test and deployment processes to be fully automated but continuous deployment also automates the release process as well 

![Screen Shot 2018-09-15 at 12.46.03](/Users/brandonpickup/Desktop/AWS_Developer/Diagrams/Screen Shot 2018-09-15 at 12.46.03.png) 



AWS Services

* CodeCommit
  * Allows you to create your own private git repo within AWS, managed source control service
* CodeBuild
  * Fully managed build service which can compile source code, run basic tests and creates software packages that are ready to deploy into your environment
* CodeDeploy
  * Can deploy code to EC2 instances, lambda functions and even servers in your  data centre
* CodePipeline
  * Continuous deployment service that allows you to store, visualise and automate your entire release process



###### Exam Tips

* Worth reading the whipper on DevOps and practicing CI/CD
* Continuous integration is about integrating or merging the code changes frequently, at least once per day, and enables multiple devs to work on the same application
* Continuous delivery is all about automating the build, test and deployment functions
* Continuous deployment fully automates the entire release process, code is deployed into production as soon as it has successfully passed through the release pipeline
* CodeCommit- source control service (git)
* CodeBuild - compile source code, run tests and package code
* CodeDeploy - automated deployment to EC2, on premises systems and Lambda
* CodePipeline - CI/CD workflow tool, fully automates the entire release process (build, test, deployment)



### CodeCommit

* Fully managed source control service that enables companies to host secure and highly scalable private git repositoires
* Git is an industry standard open source distributed source control system:
  * centralised repo for all your code, binaries, images and libraries
  * tracks and manages code changes
  * maintains version history
  * manages updates from multiple sources
  * enables collaboration
* CodeCommit provides all of the functionality of git
* Your data is encrypted in transit and at rest (commits to the remote are done by ssh or https)

###### Exam Tips

* Based on git
* Centralized repo for all your code, binaries, images and libraries 
* Tracks and manages code changes
* Maintains version history
* Maintains updates from multiple sources and enables collaboration



### Code Deploy

* AWS CodeDeploy is an automated deployment service which allows you to deploy your application code automatically to EC2 instances, on-premise systems and Lambda functions
* Allows you to quickly release new features, avoid downtime during application deployments, and avoid the risks associated with manual processes
* Automatically scales with your infrastructure and integrates with various Ci?CD tools, e.g. Jenkins, GitHub, Atlassian, AWS CodePipeline as well as config management tools like Ansible, Puppet and Chef
* Two deployment approaches are available:
  * **In Place** and 
    * The application is stopped on each instance and the latest revision is installed
    * the instance is out of service during this time and your capacity will be reduced
    * if the instances are behind a load balancer, you can configure the load balancer to stop sending requests to the instances which are being upgraded
    * Also known as a rolling update
    * it can only be used for EC2 and on-premise systems - it is not supported for Lambda
    * if you need to roll back your changes, the previous version of the application will need to be re-deployed
  * **Blue/Green**
    * new instances are provisioned and the latest revision is installed on the new instances. Blue represents the active deployment, green is the new release
    * the new instances are registered with an Elastic Load Balancer, traffic is then routed to the new instances and the original instances are eventually terminated
    * advantages of the blue/green deployments are that the new instances can be created ahead of time and the code released to production by simply switching all traffic to the new instances
    * switching back to the original environment is faster and more reliable and is just a case of routing traffic back to the original servers (as long as you have not already terminated them)
* Terminology
  * **Deployment Group** - a set of EC2 instances or Lambda functions to which a new revision of the software is to be deployed
  * **Deployment** - the process and components used to apply a new revision
  * **Deployment Configuration** - a set of deployment rules as well as success / failure conditions used during deployment
  * **AppSpec File** - defines the deployment actions you want AWS CodeDeploy to execute
  * **Revision** - everything needed to deploy the new version: AppSpec files, application files, executables and config files
  * **Application** - unique identifier for the application you want to deploy. To ensure that the correct combination of revision, deployment configuration and deployment group are referenced during the deployment

### CodePipeline

* CIs a fully managed continuous integration and continuous delivery service
* It can orchestrate the build, test and even deployment of your application every time there is a change to your code - all based on a user defined software release process
* Traditional manual approaches to code delivery can be sow and prone to errors, whereas an automated process allows developers to frequently release new features and bug fixes in a fast and reliable way
* It allows you to model your release process as a workflow or pipeline of different tasks
  * You define what happens and where for each of the different stages of the workflow, and this can be modelled using CodePipeline GUI or CLI
  * Every code change pushed to your code repository automatically enters the workflow and triggers the set of actions defined for each stage of the pipeline
  * The pipeline automatically stops if one of the stages fails  
* It integrates with CodeCommit, CodeBuild, CodeDeploy, Lambda, ElasticBeanstalk, CloudFormation, Elastic Container Service as well as third party tools like GitHub and Jenkins

###### Exam Tips

* CodePipeline is a CI/CD service
* It automates your end-to-end software release process based on a user defined workflow
* It can be configured to automatically trigger your pipeline as soon as change is detected in your source code repo
* Integrates with other services from AWS like CodeBuild and CodeDeploy, as well as third party and custom plug-ins



### AppSpec File - Lambda Deployments

* The AppSpec file is used to define the parameters that will be used for a CodeDeploy deployment. 
* The file structure depends on whether you are deploying to Lambda or EC2 or On Premises
* For lambda deployments, the AppSpec file may be written in YAML or JSON and contains the following fields:
  * **version** - reserved for future use, currently the only allowed value is 0.0
  * **resources** - the name and properties of the lambda function to deploy
  * **hooks** - specifies lambda functions to run at set points in the deployment lifecycle to validate the deployment, e.g. validation tests to run before allowing traffic to be sent to your newly deployed instances

![Screen Shot 2018-09-15 at 17.46.04](/Users/brandonpickup/Desktop/AWS_Developer/Diagrams/Screen Shot 2018-09-15 at 17.46.04.png)

 

Hooks:

* **BeforeAllowTraffic**
  * used to specify the tasks or functions you want to run before traffic is routed to the newly deployed lambda function (e.g. test to validate that the function has been deployed correctly)
* **AfterAllowTraffic**
  * used to specify the tasks or functions you want to run after the traffic has been routed to the newly deployed lambda function (e.g. test to validate that the function is accepting traffic correctly and behaving as expected)



The AppSpec file looks different for EC2 and On Premises deployments and contains the following fields:

* **version** - reserved for future use, currently the only allowed value is 0.0
* **os** - the OS version you are using, e.g. linux / windows
* **files** - the location of any application files that need to be copied and where they should be copied to (source and destination folders)
* **hooks** - lifecycle event hooks allow you to specify scripts that need to run at set points in the deployment lifecycle (e.g. to unzip application files prior to deployment, run functional tests on the newly deployed application, and to de-register and re-register instances with a load balancer)



For EC2 and On Premises deployments, the appspec.yml must be placed in the **root directory of your revision** - this is the director continuing your application source code, otherwise the deployment will fail

Typical setup looks like this:

* mywebapp folder:
  * appspec.yml
  * /Scripts
  * /Config
  * /Source



![Screen Shot 2018-09-15 at 17.54.36](/Users/brandonpickup/Desktop/AWS_Developer/Diagrams/Screen Shot 2018-09-15 at 17.54.36.png)

![Screen Shot 2018-09-15 at 17.55.24](/Users/brandonpickup/Desktop/AWS_Developer/Diagrams/Screen Shot 2018-09-15 at 17.55.24.png)



Supported hooks for EC2 and On Premises

* **BeforeBlockTraffic**
  * run tasks on instances before they are deregistered from a load balancer
* **BlockTraffic**
  * deregister instances from a load balancer
* **AfterBlockTraffic**
  * run tasks on instances after they are deregistered from a load balancer 
* **ApplicationStop**
  * gracefully stop the application for deploying the new revision
* **DownloadBundle**
  * the CodeDeploy agent copies the application revision files to a temporary location
* **BeforeInstall**
  * details of any pre-installation scripts, e.g. backing up the current version, decrypting files
* **Install**
  * the CodeDeploy agent copies the application revision files from their temp location to their current location
* **AfterInstall**
  * details of any post-installation scripts, e.g. config tasks, changing file permissions etc.
* **ApplicationStart**
  * restarts any services that were stopped during ApplicationStop
* **ValidateService**
  * details of any tests to validate the service
* **BeforeAllowTraffic**
  * run tasks on instances before they are registered with a load balancer
* **AllowTraffic**
  * register instances with a load balancer
* **AfterAllowTraffic**
  * run tasks on instances after they are registered with a load balancer



***Remember the order of these!



###### Exam Tips

* The AppSpec file defines all the parameters needed for the deployment e.g. location of application files and ore/post deployment validation tests to run
* For EC2 / On Premises systems, the appspec.yml file must be placed in the root directory of your revision (the same folder that contains your application code). It is written in YAML
* Lambda supports YAML or JSON
* The run order for hooks in a CodeDeploy deployment:
  * BeforeBlockTraffic --> BlockTraffic --> AfterBlockTraffic
  * ApplicationStop
  * BeforeInstall
  * Install
  * AfterInstall
  * ApplicationStart
  * ValidateService
  * BeforeAllowTraffic --> AllowTraffic --> AfterAllowTraffic



### Docker and CodeBuild 

* Docker is an open source tech that allows you to create applications based on either Linux or Windows containers 
  * A container is a lightweight standalone executable software package which includes everything the software needs to run - code, runtime environment, libraries, environment settings, etc. 
* AWS provides Elastic Container Service as a fully managed clustered platform which allows you to run your Docker images in the cloud
* AWS CodeBuild is a fully managed build service which runs a set of commands that you define, e.g. compiles code, runs tests and produces artefacts that are ready to deploy
* buildspec.yml is a file that describes how to build the docker file etc., basically automates the whole process that you want CodeBuild to run on your behalf that will build your docker image
  * it is optional, you are able to also do this within the GUI editor to insert build commands



###### Exam Tips

* Docker allows you to package up your software into containers which you can run in ECS
* A Docker Container includes everything the software needs to run including code, libraries, runtime and env variables 
* We use a special file called a Dockerfile to specify the instructions needed to assemble your Docker image
* Once build, Docker images can be stored in the ECR and ECS can use then use the image to launch Docker containers on your ECS cluster
* Docker commands to build, tag (apply an alias) and push your Docker image to the ECR repo
  * docker build -t myimagerepo
  * docker tag myimagerepo:latest 725350006743.dkr.ecr.eu-central-1.amazonaws.com/myimagerepo:latest
  * docker push 725350006743.dkr.ecr.eu-central-1.amazonaws.com/myimagerepo:latest
* Use  build spec.yml to define the build commands and settings used by CodeBuild to run your build
* You can override the settings in build spec.yml by adding your own commands in the console when you launch the build
* If your build fails, check the logs in the CodeBuild consoles and you can also view the full CodeBuild log in CloudWatch



### CloudFormation

* it is a service that allows you to manage, configure, and provision your AWS infrastructure as code
* resources are defined using a CloudFormation template
* CloudFormation interprets the template and makes the appropriate API calls to create resources that you have defined
* supports YAML or JSON



Benefits

* Infrastructure is provisioned consistently, with fewer mistakes (i.e. removing likely human error)
* Less time and effort than configuring things manually
* You can version control and peer review your templates
* Free to use (charged for what you create)
* Can be used to manage updates and dependencies
* Can be used to rollback and delete the entire stack as well



CloudFromation Template

* YAML or JSON templates are used to describe the end state of the infrastructure you are either provisioning or changing
* After creating the template, you upload it to CloudFormation using S3
* CloudFomration reads the template and makes the API calls on your behalf
* The resulting resources are called a stack
* **Resources** is the only mandatory section of the CloudFormation template
* Remember that the **Transform **section is used to reference additional code stored in S3, allowing for code re-use e.g. for Lambda code or template snippets / reusable pieces of CloudFormation code



###### CloudFormation Exam Tips

* CloudFormation allows you to manage, configure, and provision AWS infrastructure as code (YAML or JSON)
* Remember the main section  in the CloudFormation template:
  * Parameters: input custom values
  * Conditions: e.g. provision resources based on environemnt
  * Resources: mandatory, the AWS resources to create
  * Mappings: create custom mappings like Region:AMI
  * Transforms: reference code located in S#, e.g. Lambda code or reusable snippets of CloudFormation code



### Serverless Application Model

* SAM is an extension to CloudFormation used to define serverless applications 
* Simplified syntax for defining server less resources: APIs. Lambda Functions, DynaoDB tables etc.
* Use the SAM CLI to package your deployment code, upload it to S3 and deploy your server less application



SAM CLI Commands

* sam package (packages your app and uploads a deployment file to S3)
  * --template file
  * --output-template-file
  * --s3-bucket
* sam deploy (deploys your server less app using Cloud Formation)
  * --template-file
  * --stack-name
  * --capabilities

### Developer Theory Summary

* AWS CodeDeploy is a fully managed automated deployment service and can be used as part of the CI/CD process
* Remember the different types of deployment approaches:
  * **In-Place or Rolling update** - you stop the application on each host and deploy the latest code. EC2 and on premise systems only, To roll back you must re-deploy the previous version of the application. Will obviously have down time
  * **Blue / Green** - new instances are provisioned and the new applications is deployed o these new instances. Traffic is routed to the instances according to your own schedule. Supported for EC2, on premise systems and Lambda functions. Roll back is easy, just route the traffic back to the original instances. Blue is the active deployment, green is the new release



# 9. Advanced IAM

### Web Identity Federation

* lets you give your users access to AWS resources after they have successfully authenticated with a web-based identity provider like Amazon, Facebook or Google
* Following successful authentication, the user receives an authentication code from the Web ID provider, which they can trade for temporary AWS security credentials

Amazon Cognito provides web identity federation with the following features:

* sign-up and sign0in to your apps
* access for guest users
* acts as an identity broker between your application and Web ID providers, so you don't need to write any additional code
* synchronises user data for multiple devices
* recommended for all mobile applications AWS services

Cognito is the recommended approach for web identity federation using social media accounts like FB

* e.g. a mobile shopping application which stores user, product and order data in S3 and DynamoDB
* Cognito brokers between the app an FB and Google to provide temporary credentials which map to an IAM role allowing access to the required resources
* this means that there is no need for applications to embed or store AWS credentials locally on the device and it gives users a seamless experience across al mobile devices 

###### Exam Tips

* Federation allows users to authenticate with a web identity provider (Google, FB, Amazon)
* The user authenticates first with the web ID provider and requires an authentication token which is exchanged for temporary AWS credentials allowing them to assume an IAM role
* Cognito is an Identity Broker which handles interaction between your applications and the Web ID Provider (you don't need to write your own code to do this)
  * provides sign-up, sign-in, and guest user access
  * syncs user data for a seamless experience across your devices
  * Cognito is the AWS recommended approach for Web Identity Federation particularly for mobile apps



### Cognito User Pools

User pools are user directories used to manage sign-up and sign-in functionality for mobile and web applications

* Users can sign-in directly to the **User Pool**, or indirectly via an identity provider like FB, Amazon or Google
* Cognito acts as an Identity Broker between the ID provider and AWS
* Successful authentication generates a number of JSON Web tokens (JWTs)

Identity pools enable your to create unique identities for your users and authenticate them with identity providers

* with an identity, you can obtain temporary, limited-privilege AWS credentials to access other AWS services



Push Synchronisation

* Cognito tracks the association between user identity and the various different devices they sign-in from
* In order to provide seamless user experience for your application, Cognito uses **Push Synchronisation** to push updates and synchronise user data across multiple devices
* Amazon SNS is used to send a silent push notification to all devices associated with a given user identity whenever data stored in the cloud changes



### Inline vs Managed vs Custom Policies 

IAM is used to define user access to permissions within AWS. There are 3 different types of IAM policies available:

* **Managed Policies**
  * IAM policy which is created and administered by AWS
  * Provided for common use cases based on job function e.g. AmazonDynamoDBFullAccess, AWSCodeCommitPowerUser, AmazonEC2ReadOnlyAccess etc.
  * These allow you to assign appropriate permissions to your users groups and roles without having to write the policy yourself
  * A single managed policy can be applied to multiple users, groups or roles within the same AWS account and across different accounts
  * You **cannot change the permissions** defined in an AWS managed policy
* **Customer Managed Policies**
  * This is a standalone policy that you create and administer inside your own AWS account 
  * You can attach this policy to multiple users, groups and roles - but only within your own account
  * In order to create a customer managed policy, you can copy an existing AWS managed policy to customise it to fit the requirements of your organisation
  * Recommended for use cases where the existing AWS managed policies do not meet the needs of your environment
* **Inline Policies** 
  * IAM policy which is embedded within the user group or roles to which it applies
  * Has a strict 1:1 relationship between the entity and the policy 
  * When you delete the user, group or role in which the inline policy is embedded, the policy will also be deleted
  * In most cases, AWS recommends using managed policies over inline policies
  * These are useful when you want to be sure that the permissions in a policy are not inadvertently assigned to any other users group or role than the one for which it is intended (i.e. you are creating a policy that must only ever be attached to a single user, group or role)



###### Exam Tips

* Remember the different types of IAM policies:
* * Managed policy -  AWS managed default policies
  * Customer managed policy - Managed by your
  * Inline policy - Managed by you and the embedded i a single user, group or role
* In most cases, AWS recommends using managed policies over inline policies

 

# Useful Nuggets

Valid triggers for AWS Lambda are:

* API Gateway
* AWS IoT
* Alexa Skills Kit
* Alexa Smart Home
* CloudFront
* CloudWatch Events
* CloudWatch Logs
* CodeCommit
* Cognito Sync Trigger
* DynamoDB
* Kinesis
* S3
* SNS