# Serverless Architectures with AWS Lambda

Serverless applications are ones that don't require you to provision or manage any servers. 

For service or platform to be considered serverless, it should provide the following capabilities: 

* No server management
* Flexible scaling
* High availability
* No idle capacity

### AWS Lambda - The Basics

* Lambda is a high-scale, provision-free serverless compute offering based on functions. 
* It provides the **cloud logic layer** for your application 
* When there are multiple, simultaneous events to respond to, Lambda simply runs more copies of the function in parallel. 
* Lambda functions **scale precisely** with the size of the workload, down to the individual request 
* Architectures that use Lambda functions are designed to reduce wasted capacity. 
* Lambda can be described as a type of **serverless Function-as-a-Service (FaaS).**  
  * FaaS is one approach to building event-driven computing systems.
  * It relies on functions as the unit of deployment and execution.  
* Each Lambda function you create contains the code you want to execute, the configuration that defines how your code is executed and, optionally, one or more event sources that detect events and invoke your function as they occur. 
* You don’t need to write any code to integrate an event source with your Lambda function, manage any of the infrastructure that detects events and delivers them to your function, or manage scaling your Lambda function to match the number of events that are delivered. You can focus on your application logic and configure the event sources that cause your logic to run. 



### AWS Lambda - Diving Deeper

##### **Lambda Function Code**

* You’re free to bring any libraries, artifacts, or compiled native binaries that can execute on top of the runtime environment as part of your function code package 
* You can even execute code you’ve written in another programming language (PHP, Go, SmallTalk, Ruby, etc.), as long as you stage and invoke that code from within one of the support languages in the AWS Lambda runtime environment 
* The Lambda runtime environment is based on an Amazon Linux AMI 
* The **function code package** contains all of the assets you want to have available locally upon execution of your code.  
  * A package will, at minimum, include the code function you want the Lambda service to execute when your function is invoked 
  * However, it might also contain other assets that your code will reference upon execution, for example, additional files, classes, and libraries that your code will import, binaries that you would like to execute, or configuration files that your code might reference upon invocation. 
* When a Lambda function is invoked, code execution begins at what is called the **handler**
  *  The handler is a specific code method (Java, C#) or function (Node.js, Python) that you’ve created and included in your package. You specify the handler when creating a Lambda function
  * Each language supported by Lambda has its own requirements for how a function handler can be defined and referenced within the package. 
  * Once the handler is successfully invoked inside your Lambda function, the runtime environment belongs to the code you’ve written 
  * Your Lambda function is free to execute any logic you see fit, driven by the code you’ve written that starts in the handler.  
* When your Lambda function is invoked in one of the supported languages, one of the parameters provided to your handler function is an **event object.** 
  * The event differs in structure and contents, depending on which event source created it 
  * The contents of the event parameter include all of the data and metadata your Lambda function needs to drive its logic 
* Your Lambda function is also provided with a **context object** 
  *  allows your function code to interact with the Lambda execution environment 
  *  The contents and structure of the context object vary, based on the language runtime your Lambda function is using, but at minimum it will contain: **AWSRequestId, Remaining time, Logging**



##### Lambda Function Event Sources

There are two models for invoking a Lambda function:

1. Push Model
   * Your Lambda function is invoked every time a particular event occurs within another AWS service (for example, a new object is added to an S3 bucket). 
2. Pull Model
   * Lambda polls a data source and invokes your function with any new records that arrive at the data source, batching new records together in a single function invocation (for example, new records in an Amazon Kinesis or Amazon DynamoDB stream). 

Also, a Lambda function can be executed synchronously or asynchronously. You choose this using the parameter **InvocationType** that’s provided when invoking a Lambda function. This parameter has three possible values: 

1. **RequestResponse** – Execute synchronously. 
2. **Event** – Execute asynchronously. 
3. **DryRun** – Test that the invocation is permitted for the caller, but don’t execute the function. 



##### Lambda Function Configuration

After you write and package your Lambda function code, on top of choosing which event sources will trigger your function, you have various configuration options to set that define how your code is executed within Lambda. 

* Function memory

* Versions and aliases

  * Each and every Lambda function has a default version built in: $LATEST 

  * You can invoke each version of your Lambda function independently, at any time. Each version has its own Amazon Resource Name (ARN) 

    * ```
      arn:aws:lambda:[region]:[account]:function:[fn_name]:[version] 
      ```

  * Lambda function container is specific to a particular version of your function. So, for example, if there are already several function containers deployed and available in the Lambda runtime environment for version 5 of the function, version 6 of the same function will not be able to execute on top of the existing version 5 containers—a different set of containers will be installed and managed for each function version 

* IAM role

  * In the context of Lambda, you assign an IAM role (called an execution role) to each of your Lambda functions .
  *  IAM policies attached to that role define what AWS service APIs your function code is authorized to interact with 
  * Benefits:
    * Your source code isn’t required to perform any AWS credential management or rotation to interact with the AWS APIs. Simply using the AWS SDKs and the default credential provider results in your Lambda function automatically using temporary credentials associated with the execution role assigned to the function. 
    * Your source code is decoupled from its own security posture. If a developer attempts to change your Lambda function code to integrate with a service that the function doesn’t have access to, that integration will fail due to the IAM role assigned to your function 

* Lambda function permissions

  * You can define which push model event sources are allowed to invoke a Lambda function through a concept called **permissions.**  
  * With permissions, you declare a function policy that lists the AWS Resource Names (ARNs) that are allowed to invoke a function. 
  * For pull model event sources (for example, Kinesis streams and DynamoDB streams), you need to ensure that the appropriate actions are permitted by the IAM execution role assigned to your Lambda function. 

* Network Configuration

  * Default vs VPC

* Environment variables

  * Software Development Life Cycle (SDLC) best practice dictates that developers separate their code and their config. 
  * Environment variables for Lambda functions enable you to dynamically pass data to your function code and libraries without making changes to your code. 
  * By default, these variables are encrypted at rest. 
    * For any sensitive information that will be stored as a Lambda function environment variable, we recommend you encrypt those values using the AWS Key Management Service (AWS KMS) prior to function creation, storing the encrypted cyphertext as the variable value 
  * Dead letter queues
    * If an exception occurs when trying to invoke your function in these models, the invocation will be attempted two more times (with back-off between the retries). After the third attempt, the event is either discarded or placed onto a dead letter queue, if you configured one for the function 
    * A dead letter queue is either an SNS topic or SQS queue that you have designated as the destination for all failed invocation events. If a failure event occurs, the use of a dead letter queue allows you to retain just the messages that failed to be processed during the event. Once your function is able to be invoked again, you can target those failed events in the dead letter queue for reprocessing. 
  * Timeout
    * Because your Lambda function is billed based on execution time in 100-ms increments, avoiding lengthy timeouts for functions can prevent you from being billed while a function is simply waiting to timeout 



### Serverless Best Practices

##### ***Security Best Practices***

* One IAM role per function
  * Each and every Lambda function within your AWS account should have a 1:1 relationship with an IAM role 
* Temporary AWS Credentials
  * You should not have any long-lived AWS credentials included within your Lambda function code or configuration. 
* Persisting secrets
  * There are cases where you may have long-lived secrets (for example, database credentials, dependency service access keys, encryption keys, etc.) that your Lambda function needs to use. We recommend a few options for the lifecycle of secrets management in your application: 
  * **Lambda Environment Variables with Encryption Helpers**
    * Advantages – Provided directly to your function runtime environment, minimizing the latency and code required to retrieve the secret. 
    * Disadvantages – Environment variables are coupled to a function version. Updating an environment variable requires a new function version (more rigid, but does provide stable version history as well) 
  * **Amazon EC2 Systems Manager Parameter Store**
    * Advantages – Fully decoupled from your Lambda functions to provide maximum flexibility for how secrets and functions relate to each other. 
    * Disadvantages – A request to Parameter Store is required to retrieve a parameter/secret. While not substantial, this does add latency over environment variables as well as an additional service dependency, and requires writing slightly more code 
* Using secrets
  * Secrets should always only exist in memory and never be logged or written to disk 
* API Authorization
  *  API Gateway can perform much of the heavy lifting by providing things like native AWS SigV4 authentication, generated client SDKs, and custom authorizers.
* VPC Security
  * If your Lambda function requires access to resources deployed inside a VPC, you should apply network security best practices through use of least privilege security groups, Lambda function-specific subnets, network ACLs, and route tables that allow traffic coming only from your Lambda functions to reach intended destinations. 
* Deployment Access Control 
  *  Making code changes to a Lambda function should be achieved through automation 



***Reliability Best Practices***

- High Availability
  - The availability posture of your Lambda function depends on the number of Availability Zones it can be executed in. 
  -  If your function uses the default network environment, it is automatically available to execute within all of the Availability Zones in that AWS Region. Nothing else is required to configure high availability for your function in the default network environment 
  - If your function is deployed within your own VPC, the subnets (and their respective Availability Zones) define if your function remains available in the event of an Availability Zone outage. 
- Fault tolerance
  - In the end, a multi-region architecture is very application specific. The most important thing to do to make a multi-region design feasible is to account for it in your design up front. 
- Recovery
  - Consider how your serverless application should behave in the event that your functions cannot be executed. For use cases where API Gateway is used as the event source, this can be as simple as gracefully handling error messages and providing a viable, if degraded, user experience until your functions can be successfully executed again. 
  - For asynchronous use cases, it can be very important to still ensure that no function invocations are lost during the outage period. To ensure that all received events are processed after your function has recovered, you should take advantage of dead letter queues and implement how to process events placed on that queue after recovery occurs. 



##### ***Performance Efficiency Best Practices***

- Choosing the Optimal Memory Size 
  - Lambda provides a single dial to turn up and down the amount of compute resources available to your function—the amount of RAM allocated to your function 
  - We recommend that you test your Lambda function at each of the available resource levels to determine what the optimal level of price/performance is for your application. You’ll discover that the performance of your function should improve logarithmically as resource levels are increased. 
  - However, pricing increases linearly as the resource levels increase in Lambda. 
  - Your tests should find where the logarithmic function bends to choose the optimal configuration for your function 
  - Simply choosing the smallest resource amount that runs your function adequately fast is an anti-pattern. Because Lambda is billed in 100-ms increments, this strategy might not only add latency to your application, it might even be more expensive overall if the added latency outweighs the resource cost savings. 
- Language Runtime Performance
  - The compiled languages (Java and .NET) incur the largest initial startup cost for a container’s first invocation, but show the best performance for subsequent invocations 
  - The interpreted languages (Node.js and Python) have very fast initial invocation times compared to the compiled languages, but can’t reach the same level of maximum performance as the compiled languages do. 
  - If your application use case is both very latency-sensitive and susceptible to incurring the initial invocation cost frequently (very spiky traffic or very infrequent use), we recommend one of the interpreted languages 
- Optimizing Your Code
  - examples of how you can improve the performance of your function code when a warm container is invoked: 
    - After initial execution, store and reference any externalized configuration or dependencies that your code retrieves locally 
    - Limit the reinitialization of variables/objects on every invocation (use global/static variables, singletons, etc.). 
    - Keep alive and reuse connections (HTTP, database, etc.) that were established during a previous invocation. 
  -  you should do the following to limit the amount of time that a cold start takes for your Lambda function: 
    - Always use the default network environment unless connectivity to a resource within a VPC via private IP is required 
    - Choose an interpreted language over a compiled language 
    - Trim your function code package to only its runtime necessities. This reduces the amount of time that it takes for your code package to be downloaded from Amazon S3 ahead of invocation 



##### ***Operational Excellence Best Practices***

- Logging
  - For deployed functions, you depend heavily on the logs you create to inform an investigation of function behavior. 
- Metrics and Monitoring 
  - It’s best practice to create alarm thresholds (high and low) for each of your Lambda functions on all of the provided metrics through CloudWatch. 
  - A major change in how your function is invoked or how long it takes to execute could be your first indication of a problem in your architecture. 
- Deployment
  - Parallel version invocations – Updating an alias to point to a new version of a Lambda function happens asynchronously on the service side. There will be a short period of time that existing function containers containing the previous source code package will continue to be invoked alongside the new function version the alias has been updated to. 
  - Deployment schedule – Performing a Lambda function deployment during a peak traffic time could result in more cold start times than desired. You should always perform your function deployments during a low traffic period to minimize the immediate impact of the new/cold function containers being provisioned in the Lambda environment. 
- Load Testing
  - It’s important to analyze how long your function runs so that you can better determine any problems with a dependency service that might increase the concurrency of the function beyond what you expect.  



##### ***Cost Optimization Best Practices***

- Because Lambda charges are based on function execution time and the resources allocated, optimizing your costs is focused on optimizing those two dimensions. 
- Right-sizing
  - If your function’s resource size is too small, you could pay more due to a longer execution time than if more resources were available that allowed your function to complete more quickly. 
- Distributed and Asynchronous Architectures 
  - If you are able to design your application to be asynchronous, you might find that each decoupled component of your architecture takes less compute time to conduct its work than tightly coupled components that spend CPU cycles awaiting responses to synchronous requests. 
- Batch size
  - Some Lambda event sources allow you to define the batch size for the number of records that are delivered on each function invocation (for example, Kinesis and DynamoDB). You should test to find the optimal number of records for each batch size so that the polling frequency of each event source is tuned to how quickly your function can complete its task. 
- Event Source Selection



##### Serverless Development Best Practices

*Infrastructure as Code – the AWS Serverless Application Model (AWS SAM)*

 AWS SAM is an open specification abstraction layer on top of AWS CloudFormation. It provides a set of command line utilities that enable you to define a full serverless application stack with only a handful of lines of JSON or YAML, package your Lambda function code together with that infrastructure definition, and then deploy them together to AWS 



*Local Testing – AWS SAM Local*

 AWS SAM Local offers additional command line tools that you can add to AWS SAM to test your serverless functions and applications locally before deploying them to AWS 

AWS SAM Local uses Docker to enable you to quickly test your developed Lambda functions using popular event sources (for example, Amazon S3, DynamoDB, etc.).  



##### ***Coding and Code Management Best Practices***

* Coding Best Practices
  * Depending on the Lambda runtime language you build with, continue to follow the best practices already established for that language. While the environment that surrounds how your code is invoked has changed with Lambda, the language runtime environment is the same as anywhere else 
  * **Business logic outside handlers - ** Lambda function starts execution at the handler function you define within your code package. Within your handler function you should receive the parameters provided by Lambda, pass those parameters to another function to parse into new variables/objects that are contextualized to your application, and then reach out to your business logic that sits outside the handler function and file. This enables you to create a code package that is as decoupled from the Lambda runtime environment as possible 
  * **Warm Containers -** you should write code that takes advantage of a warm function container. This means scoping your variables in a way that they and their contents can be reused on subsequent invocations where possible. 
  * **Control Dependencies -** To enable the latest set of features and security updates, Lambda periodically updates these libraries. These updates can introduce subtle changes to the behavior of your Lambda function. To have full control of the dependencies your function uses, we recommend packaging all your dependencies with your deployment package. 
  * **Fail Fast - ** reasonably short timeouts for any external dependencies, as well as a reasonably short overall Lambda function timeout. Don’t allow your function to spin helplessly while waiting for a dependency to respond. 
* Code Management Best Practices
  * **Code Repository Organization - ** We recommend that you organize your Lambda function source code to be very fine-grained within your source code management solution of choice. This usually means having a 1:1 relationship between Lambda functions and code repositories or repository projects.  . Each Lambda function should be fully decoupled from a source code perspective from other Lambda functions, just as it will be when it’s deployed. Y 
  * **Release Branches - ** We recommend that you create a repository or project branching strategy that enables you to correlate Lambda function deployments with incremental commits on a release branch 



### ***Testing***

* Unit Tests
  * With what we’ve said earlier in mind, we recommend that you unit test your Lambda function code thoroughly, focusing mostly on the business logic outside your handler function 
  * You should also unit test your ability to parse sample/mock objects for the event sources of your function. 
  * Also, to supplement the unit tests you’ve written, you should create local test automation using AWS SAM Local that can serve as local end-to-end testing of your function code 
* Integration Testing 
  * For integration tests, we recommend that you create lower lifecycle versions of your Lambda functions where your code packages are deployed and invoked through sample events that your CI/CD pipeline can trigger and inspect the results of 



### ***Continuous Delivery***

* **AWS CodeCommit –** Provides hosted private Git repositories that will enable you to host your serverless source code, create a branching strategy that meets our recommendations (including fine-grained access control), and integrate with AWS CodePipeline to trigger a new pipeline execution when a new commit occurs in your release branch. 
* **AWS CodePipeline –** Defines the steps in your pipeline. Typically an AWS CodePipeline pipeline begins where your source code changes arrive. Then you execute a build phase, execute tests against your new build, and perform a deployment and release of your build into the live environment. AWS CodePipeline provides native integration options for each of these phases with other AWS services 
* **AWS CodeBuild –** Can be used for the build state of your pipeline. Use it to build your code, execute unit tests, and create a new Lambda code package. Then, integrate with AWS SAM to push your code package to Amazon S3 and push the new package to Lambda via AWS CloudFormation. 
* **AWS CodeStar –** A unified user interface for creating a serverless application (and other types of applications) that helps you follow these best practices from the beginning. When you create a new project in AWS CodeStar, you automatically begin with a fully implemented and integrated continuous delivery toolchain (using AWS CodeCommit, AWS CodePipeline, and AWS CodeBuild services mentioned earlier). You will also have a place where you can manage all aspects of the SDLC for your project, including team member management, issue tracking, development, deployment, and operations 











