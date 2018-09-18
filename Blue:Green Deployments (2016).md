# Blue/Green Deployments on AWS [2016]

### Introduction

In a traditional approach to application deployment, you typically fix a failed deployment by redeploying an older, stable version of the application.  

* This is typically done on the same set of resources due to the cost and effort of provisioning additional resources.  
* While this approach works, it also has many shortcomings;
  * Rollback isn’t easy because it’s implemented by redeployment of an older version from scratch  (which takes time, hence downtime)
  * Even in situations where the application is only impaired, a rollback is required which will overwrites the faulty version (so there is no opportunity to debug the fault application in place)



**Blue/Green Deployment Methodology**

Blue/green deployments provide near zero-downtime release and rollback capabilities. The fundamental idea behind blue/green deployment is to shift traffic between two identical environments that are running different versions of your application.  

* The blue environment represents the current application version serving production traffic  
* In parallel, the green environment is staged running a different version of your application  
* After the green environment is ready and tested, production traffic is redirected from blue to green.  
* If any problems are identified, you can roll back by reverting traffic back to the blue environment.  

Although blue/green deployment isn’t a new concept, you don’t commonly see it used in traditional, on-premises hosted environments due to the cost and effort required to provision additional resources.  



**Benefits of Blue/Green**

* Blue/green deployments provide a level of isolation between your blue and green application environments

  * This isolation reduces your deployment risk  
  * After you deploy the green environment, you have the opportunity to validate it.  
  * You might do that with test traffic before sending production traffic to the green environment, or by using a very small fraction of production traffic, to better reflect real user traffic. This is called *canary analysis* or *canary testing*.  
  * If you discover the green environment is not operating as expected, there is no impact on the blue environment.  
  * It ensures spinning up a parallel green environment does not affect resources underpinning your blue environment.  

* You can roll back to the blue environment at any time during the deployment process. 

  * Impaired operation or downtime is minimized because impact is limited to the window of time between green environment issue detection and shift of traffic back to the blue environment.  
  * Furthermore, impact is limited to the portion of traffic going to the green environment, not all traffic.  

* Blue/green deployments also fit well with continuous integration and continuous deployment (CI/CD) workflows, in many cases limiting their complexity.  

  * Your deployment automation would have to consider fewer dependencies on an existing environment, state, or configuration.  

* In AWS, blue/green deployments also provide cost optimization benefits. 

  * You’re not tied to the same underlying resources  
  * So if the performance envelope of the application changes from one version to another, you simply launch the new environment with optimized resources, whether that means fewer resources or just different compute resources.  
  * During the deployment, you can scale out the green environment as more traffic gets sent to it and scale the blue environment back in as it receives less traffic.  
  * Once the deployment succeeds, you decommission the blue environment and stop paying for the resources it was using.  

  

**AWS Tools and Services Enabling Blue/Green Deployments**

* Amazon Route 53
  * Amazon Route 53 is a highly available and scalable authoritative DNS service that routes user requests for Internet-based resources to the appropriate destination. 
  * DNS is a classic approach to blue/green deployments, allowing administrators to direct traffic by simply updating DNS records in the hosted zone.  
* Elastic Load Balancing
  * Elastic Load Balancing distributes incoming application traffic across designated Amazon Elastic Compute Cloud (Amazon EC2) instances. 
  * Elastic Load Balancing scales in response to incoming requests, performs health checking against Amazon EC2 resources, and naturally integrates with other AWS tools, such as Auto Scaling.  
* Auto Scaling
  * Auto Scaling helps maintain application availability and lets customers scale EC2 capacity up or down automatically according to defined conditions. 
  * You can attach different versions of launch configuration to an Auto Scaling group to enable blue/green deployment  
* Elastic Beanstalk
  * Elastic Beanstalk makes it easy to run multiple versions of your application and provides capabilities to swap the environment URLs, facilitating blue/green deployment.  
* AWS OpsWorks
  * AWS OpsWorks is a configuration management service based on Chef that allows customers to deploy and manage application stacks on AWS. 
  * OpsWorks simplifies cloning entire stacks when you’re preparing blue/green environments.  
* CloudFormation
  * AWS CloudFormation provides customers with the ability to describe the AWS resources they need through JSON formatted templates. 
  * This service provides very powerful automation capabilities for provisioning blue/green environments and facilitating updates to switch traffic, whether through Route 53 DNS, Elastic Load Balancing, etc.  
* CloudWatch
  * CloudWatch can collect and track metrics, collect and monitor log files, and set alarms. 
  * It provides system-wide visibility into resource utilization, application performance, and operational health, which are key to early detection of application health in blue/green deployments  



**Tecniques**

Full details on each of the techniques are available in the white paper 

* Update DNS Routing with Amazon Route 53  
  * This approach works with a wide variety of environment configurations, as long as you can express the endpoint into the environment as a DNS name or IP address.  
  * You can shift traffic all at once or you can do a weighted distribution. With Amazon Route 53, you can define a percentage of traffic to go to the green environment and gradually update the weights until the green environment carries the full production traffic.  `
* Swap the Auto Scaling Group Behind Elastic Load Balancer  
  * As you scale up the green Auto Scaling group, you can take blue Auto Scaling group instances out of service by either terminating them or putting them in Standby state
  * Standby is a good option because if you need to roll back to the blue environment, you only have to put your blue server instances back in service and they're ready to go. 
* Update Auto Scaling Group Launch Configurations  
  * After a new launch configuration is in place, any new instances that are launched use the new launch configuration parameters, but existing instances are not affected.  
* Swap the Environment of an Elastic Beanstalk Application  
  * The existing environment’s configuration is copied and used to launch the green environment with the new version of the application. The new—green—environment will have its own URL. When it’s time to promote the green environment to serve production traffic, you can use Elastic Beanstalk's Swap Environment URLs feature
* Clone a Stack in AWS OpsWorks and Update DNS  
  * AWS OpsWorks has the concept of stacks, which are logical groupings of AWS resources (EC2 instances, Amazon RDS, Elastic Load Balancing, and so on) that have a common purpose and should be logically managed together. Stacks are made of one or more layers.  
  * When it’s time to promote the green environment/stack into production, update DNS records to point to the green environment/stack’s load balancer. You can also do this DNS flip gradually by using the Amazon Route 53 weighted routing policy  



**Best Practices for Managing Data Synchronization and Schema Changes**  

Both the blue and green environments need up-to-date data:  

* The green environment needs up-to-date data access because it’s becoming the new production environment.  
*  The blue environment needs up-to-date data in the event of a rollback, when production is then either shifted back or kept on the blue environment.  

Broadly, you accomplish this by having both the green and blue environments share the same data stores. Unstructured data stores, such as Amazon Simple Storage Service (Amazon S3) object storage, NoSQL databases, and shared file systems are often easier to share between the two environments.  

Structured data stores, such as relational database management systems (RDBMS), where the data schema can diverge between the environments, typically require additional considerations.  





**Decoupling Schema Changes from Code Changes** 

A general recommendation is to decouple schema changes from the code changes. This way, the relational database is outside of the environment boundary defined for the blue/green deployment and shared between the blue and green environments. The two approaches for performing the schema changes are often used in tandem:  

* The schema is **changed first**, before the blue/green code deployment. Database updates must be backward compatible, so the old version of the application can still interact with the data  
* The schema is **changed last**, after the blue/green code deployment. Code changes in the new version of the application must be backward compatible with the old schema.  



Schema modifications in the former approach are often additive. You add fields to tables, new entities, and relationships. If needed, you can use triggers or asynchronous processes to populate these new constructs with data based on data changes performed by the old application version.  

Schema modifications in the latter approach are often deletive. You remove unneeded fields, entities, and relationships, or merge and consolidate them. By this time, the old application version is no longer operational.  



**When Blue/Green Deployments Are Not Recommended**  

* Are your schema changes too complex to decouple from the code changes? Is sharing of data stores not feasible?  
  * All these situations require a solution where the data store is inside the deployment environment boundary and tightly coupled to the blue and green applications, respectively.  
* Does your application need to be “deployment aware”?  
* Does your commercial off-the-shelf (COTS) application come with a predefined update/upgrade process that isn’t blue/green deployment friendly?  





Application deployment has associated risks. But the advent of cloud computing, deployment and automation frameworks, and new deployment techniques, such as blue/green, help mitigate risks, such as human error, process, downtime, and rollback capability. The AWS utility billing model and breadth of automation tools make it much easier for customers to move fast and cost-effectively implement blue/green deployments at scale.  