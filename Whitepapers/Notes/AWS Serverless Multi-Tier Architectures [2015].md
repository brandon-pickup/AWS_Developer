# AWS Serverless Multi-Tier Architectures [2015]

### Introduction

Multi-tiered applications are often built using a service-oriented architecture (SOA) approach to using web services. In this approach, the network acts as the boundary between tiers 

Amazon API Gateway, a service for creating and managing APIs, and AWS Lambda, a service for running arbitrary code functions, can be used together to simplify the creation of robust multi-tier applications 

- Regardless of the request volume required, both the API Gateway and Lambda will **scale automatically** to support exactly the needs of your application.  
- When combined, you can create a tier for your application that allows you to write the code that matters to your application and not focus on various other undifferentiating aspects of implementing a multi-tiered architecture 

The integration of Lambda and Amazon VPC has indirectly expanded the capabilities of Amazon API Gateway because it gives developers the ability to define their own set of Internet-accessible HTTPS APIs in front of a backend that remains private and secure as part of Amazon VPC 



### Three-tier Architecture Overview

The three-tier architecture is a popular pattern for user-facing applications. The tiers that comprise this architecture include the 

- the **presentation** tier
  - represents the component that users directly interact with (such as a web page, mobile app UI, etc.).
- the **logic** tier
  - contains the code required to translate user actions at the presentation tier to the functionality that drives the application’s behavior.
- and the **data** tier 
  - consists of storage media (databases, object stores, caches, file systems, etc.) that hold the data relevant to the application. 



### The Serverless Logic Tier

The logic tier of the three-tier architecture represents the brains of the application. 

This is why integrating Amazon API Gateway and AWS Lambda to form your logic tier can be so revolutionary. The features of the two services allow you to build a serverless production application that is highly available, scalable, and secure. 

Your application could use thousands of servers, however by leveraging this pattern you do not have to manage a single one. In addition, by using these managed services together you gain the following benefits: 

- No OSs to secure, patch or manage
- No servers to right size, monitor or scale out
- No risk to your cost by over-provisioning
- No risk to your performance by under-provisioning



##### Amazon API Gateway

Amazon API Gateway is a fully managed service for defining, deploying, and maintaining APIs.  It has specific features and qualities that result it being a powerful edge for your logic tier. 

- Integration with AWS Lambda
  - API Gateway forms the bridge that connects your presentation tier and the functions you write in AWS Lambda. 
  - After defining the client/server relationship using your API, the contents of the client’s HTTPS request are passed to Lambda function for execution. Those contents include request metadata, request headers, and the request body. 
- Stable API performance across the globe
  - Each deployment of Amazon API Gateway includes an Amazon CloudFront distribution under the covers. Amazon CloudFront is a content delivery web service that uses Amazon’s global network of edge locations as connection points for clients integrating with your API 
  - This helps drive down the total response time latency of your API. Through its use of multiple edge locations across the world, Amazon CloudFront also provides you capabilities to combat distributed denial of service (DDoS) attack scenarios 
  - You can improve the performance of specific API requests by using Amazon API Gateway to store responses in an optional in-memory cache. This not only provides performance benefits for repeated API requests, but it also reduces backend executions, which can reduce your overall cost 
- Encourage Innovation
  - The development work required to build any new application is an investment. You need to justify that in order for the project to begin. By reducing the amount of investment required for development tasks and time, you are free to more experiment and innovate more freely 
- Iterate Rapidly, Stay Agile
  - With new applications, the user base may still be poorly defined (size, usage patterns, etc.). The logic tier must remain agile while the user base takes shape.  
  - API Gateway and AWS Lambda enable versioning so that existing functionality and client dependencies can continue undisturbed while new functionality is released as a separate API/function version. 
- Security
  - Implementing the logic tier of a public three-tier web application as a web service immediately elevates the topic of security. 
  - The application needs to ensure that only authorized clients have access to your logic tier (which is exposed over the network). 
  - You can take advantage of several different ways in which Amazon API Gateway contributes to securing your logic tier:
    - All requests to your APIs can be made via HTTPS to enable encryption in transit 
    - Your AWS Lambda functions can restrict access so that there is a trust relationship only between a particular API within Amazon API Gateway and a particular function in AWS Lambda. There will be no other way to invoke that Lambda function except by using the API through which you’ve chosen to expose it 
    - The Amazon API Gateway allows you to generate client SDKs to integrate with your APIs. That SDK also manages the signing of requests when APIs require authentication. 
    - Each resource/method combination that you create as part of your API is granted its own specific Amazon Resource Name (ARN) that can be referenced in AWS Identity and Access Management (IAM) policies.



##### AWS Lambda

At its core, AWS Lambda allows arbitrary code written in any of the supported languages (Node, JVM based, and Python as of November 2015) to be triggered in response to an event. That event can be one of several programmatic triggers that AWS makes available, called an event source.

- Your business logic goes here
  - AWS Lambda allows you to write code functions, called handlers, which will execute when triggered by an event 
  - Lambda allows you to create modular handlers at your chosen level of granularity (one per API or one per API method) that can be updated, invoked, and changed independently. 
  - The handler is then free to reach out to any other dependencies it has (such as other functions you’ve uploaded with your code, libraries, native binaries, or even external web services). 
  - Lambda allows you to package all of your required dependencies in your function definition during creation. When you create your function, you specify which method inside your deployment package will act as the request handler.  
- Amazon VPC Integration
  - AWS Lambda, the core of your logic tier, will be the component directly integrating with the data tier. 
  - For AWS services with which you can integrate from a Lambda function, you can manage access control using IAM policies.  
  - The use of a VPC means the databases and other storage media that your business logic depends on can be made inaccessible over the Internet  
  -  The VPC also ensures that the only way to interact with your data from the Internet will be through the APIs that you’ve defined and the Lambda code functions that you’ve written  
- Security
  - To execute a Lambda function, it must be triggered by an event or service that has been permitted to do so via an IAM policy 
    - It is possible to create a Lambda function that cannot be executed at all unless it is invoked by an API Gateway request that you define 
  - Each Lambda function itself assumes an IAM role, an ability that must be granted via an IAM trust relationship. That IAM role defines the other AWS services/resources your Lambda function will be able to interact with (such as an Amazon DynamoDB table or an Amazon S3 bucket). 
  - The services your function has access to will be defined and controlled from outside of the function itself. This is subtle, but powerful. It allows the code you write to be free from storing or retrieving AWS credentials: This means you don’t have to hard code API keys, and you don’t have to write code to retrieve them and store them in memory 



### The Data Tier

By using AWS Lambda as your logic tier, you have a wide number of data storage options for your data tier. These options fall into two broad categories: 

1. Amazon VPC hosted data stores and 
2. IAM-enabled data stores. 

AWS Lambda has the ability to securely integrate with both. 



##### Amazon VPC Hosted Data Stores

The integration of AWS Lambda with Amazon VPC enables functions to integrate with a variety of data storage technologies in a private and secure manner. 

- Amazon RDS
  - Use any of the engines that Amazon Relational Database Service (Amazon RDS) makes available. Connect to Amazon RDS directly from the code you’ve written in Lambda just as you would outside of Lambda, but with the advantage of simple integration with the AWS Key Management Service (AWS KMS) for database credential encryption 
- Amazon ElastiCache
  - Integrate your Lambda functions with a managed in-memory cache to boost the performance of your application.
- Amazon RedShift
  - You can build functions that securely query an enterprise data warehouse for the purpose of building reports, dashboards, or retrieving ad-hoc query results 



##### IAM Enabled Data Stores

Because AWS Lambda is integrated with IAM, it can use IAM for securing integration with any AWS service that can be leveraged directly using the AWS APIs 

- Amazon DynamoDB
  - Amazon DynamoDB is the AWS infinitely scalable NoSQL database. Consider Amazon DynamoDB when you want to retrieve data records (400KB or smaller as of this writing) with single-digit millisecond performance, regardless of scale.  
  - Using Amazon DynamoDB finegrained access control your Lambda functions can follow the best practice of least privilege when querying specific data in DynamoDB 
- Amazon S3
  - Objects stored in Amazon S3 (files, images, logs, any binary data) can be accessed directly via HTTP. Lambda functions can communicate securely with Amazon S3 via virtual private endpoints, and data within S3 can be restricted to only the IAM policy associated with the Lambda function 
- Amazon Elasticsearch Service
  - Amazon Elasticsearch Service (Amazon ES) is a managed version of the popular search and analytics engine, Elasticsearch 
- Amazon ES provides managed provisioning of clusters, failure detection, and replacement of nodes; you can restrict access to the Amazon ES API by using IAM policies. 





### The Presentation Tier

Amazon API Gateway opens up a variety of presentation tier possibilities. An Internet-accessible HTTPS API can be consumed by any client capable of HTTPS communication 

- Mobile App: In addition to integrating with custom business logic via Amazon API Gateway and AWS Lambda, you could use Amazon Cognito as a mechanism to create and manage user identities. 
- Static website content (such as files hosted in Amazon S3): You can enable your Amazon API Gateway APIs to be cross-origin resource sharing (CORS)-compliant. This allows web browsers to directly invoke your APIs from within the static web pages. 
- Any other HTTPS-enabled client device: Many connected devices are capable of communicating via HTTPS. There is nothing unique or proprietary about how clients communicate with the APIs you create using Amazon API Gateway; it is pure HTTPS. No specific client software or licenses are required. 



