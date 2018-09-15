# 4. Intro to Serverless Computing

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
* Know whatservices are serverless: API Gateway, S3, DynamoDB
  * RDS, EC2 not serverless
* Lambda functions can trigger other lambda functions, i.e. 1 event can = x functions if there are triggers on other functions
* AWS xray can help debug what is happening
* Can do things globally, it is not stuck in one specific region. i.e. you can back up S3 buckets into other S3 buckets
* Know your triggers



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
* You can throttle API Gateway to prevent attacks
* You can log results to CLoudWatch
* If you are using Javascript/AJAX that uses multiple domains with API Gateway, ensure that you have enable CORS 
* CORS is enforced by the client



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



For EC2 and On Premises deployments, the app spec.yml must be placed in the **root directory of your revision** - this is the director continuing your application source code, otherwise the deployment will fail

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