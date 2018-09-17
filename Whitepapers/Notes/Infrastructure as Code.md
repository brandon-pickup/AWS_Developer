# Infrastructure as Code [2017]

DevOps is the combination of cultural philosophies, practices, and tools that increases your organisation’s ability to deliver applications and services at high velocity. 



### Intro to Infrastructure as Code

Cloud computing takes advantage of virtualisation to enable the on-demand provisioning of compute, network, and storage resources that constitute technology infrastructures. 

The manual processes have certain disadvantages, including: 

* Higher cost as a result of unnecessary human capital
* Human error means inconsistency and thus deviation rom 
* Lack of agility
* Difficulty in attaining and maintaining compliance to corporate or industry standards due to the absence of repeatable processes

Infrastructure as Code addresses these deficiencies by bringing automation to the provisioning process. Rather than relying on manually performed steps, both administrators and developers can instantiate infrastructure using configuration files 	

Infrastructure as Code eliminates configuration drift through automation, thereby increasing the speed and agility of infrastructure deployments. 



### Infrastructure Resource Lifecycle

![Screen Shot 2018-09-17 at 16.23.24](/Users/brandonpickup/Desktop/AWS_Developer/Diagrams/Screen Shot 2018-09-17 at 16.23.24.png)

Each stage involves procedures that can leverage code. This extends the benefits of Infrastructure as Code from its traditional role in provisioning to the entire resource lifecycle. Every lifecycle then benefits from the consistency and repeatability that Infrastructure as Code offers. 



### Resource Provisioning

The information resource lifecycle begins with resource provisioning. Scenarios have one thing in common: the need for a repeatable process for instantiating resources consistently. To address this need, AWS offers AWS CloudFormation. 

<u>*AWS CloudFormation*</u>

* AWS CloudFormation gives developers and systems administrators an easy way to create, manage, provision, and update a collection of related AWS resources in an orderly and predictable way. 
* Uses templates written in JSON or YAML format to describe the collection of AWS resources (known as a stack), their associated dependencies, and any required runtime parameters. 
* You can use a template repeatedly to create identical copies of the same stack consistently across AWS Regions 
* After deploying the resources, you can modify and update them in a controlled and predictable way. In effect, you are applying version control to your AWS infrastructure the same way you do with your application code. 
* Change Sets
  * You can update AWS CloudFormation templates with application source code to add, modify, or delete stack resources. The change sets feature enables you to preview proposed changes to a stack without performing the associated updates 
  * The change sets capability allows you to go beyond version control in AWS CloudFormation by enabling you to keep track of what will actually change from one version to the next 
  * Developers and administrators can gain more insight into the impact of changes before promoting them and minimize the risk of introducing errors 
  * The nested stack feature allows you to create a new AWS CloudFormation stack resource within an AWS CloudFormation template and establish a parent-child relationship between the two stacks. 
* Temaplate Linting
  * As with application code, AWS CloudFormation templates should go through some form of static analysis, also known as linting 
  * The goal of linting is to determine whether the code is syntactically correct, identify potential errors, and evaluate adherence to specific style guidelines. 
  * AWS CloudFormation provides the ValidateTemplate API that checks for proper JSON or YAML syntax 



AWS CloudFormation provides a template-based way of creating infrastructure and managing the dependencies between resources during the creation process. With AWS CloudFormation, you can maintain your infrastructure just like application source code. 



### Configuration Management

In environments that have high levels of durability, it might be preferable to have ways to make incremental changes to the current resources instead of reprovisioning them. To address this need, AWS offers Amazon EC2 Systems Manager and AWS OpsWorks for Chef Automate.  

Amazon EC2 Systems Manager is a collection of capabilities that simplifies common maintenance, management, deployment, and execution of operational tasks on EC2 instances and servers or virtual machines (VMs) in on-premises environments.  

* These capabilities help with automating complex and repetitive tasks, defining system configurations, preventing drift, and maintaining software compliance  



AWS OpsWorks for Chef Automate brings the capabilities of Chef, a configuration management platform, to AWS.  

* OpsWorks for Chef Automate further builds on Chef’s capabilities by providing additional features that support DevOps capabilities at scale.  
* Chef recipes, like AWS CloudFormation templates, are a form of source code that can be version controlled, thereby extending the principle of Infrastructure as Code to the configuration management stage of the resource lifecycle  



Amazon EC2 Systems Manager lets you deploy, customize, enforce, and audit an expected state configuration to your EC2 instances and servers or VMs in your on-premises environment. AWS OpsWorks for Chef Automate enables you to use Chef recipes to support the configuration of an environment. You can use OpsWorks for Chef Automate independently or on top of an environment provisioned by AWS CloudFormation. The run documents and policies associated with Systems Manager and the recipes associated with OpsWorks for Chef Automate can become part of the infrastructure code base and be controlled just like application source code. 

### Monitoring and Performance

It’s important to capture key metrics to assess the health of the environment and take corrective action when problems arise. Metrics provide visibility.  

* With metrics, your organization can respond automatically to events. 
* Without metrics, your organization is blind to what is happening in its infrastructure, thereby requiring human intervention to address all issues.

With scalable and loosely coupled systems written in multiple languages and frameworks, it can be difficult to capture the relevant metrics and logs and respond accordingly. To address this need, AWS offers the Amazon CloudWatch services. 

*  Amazon CloudWatch is a set of services that ingests, interprets, and responds to runtime metrics, logs, and events. 
* CloudWatch automatically collects metrics from many AWS services
* CloudWatch consists of three services: 
  * the main CloudWatch service
    * collects and tracks metrics for many AWS services such as Amazon EC2, ELB, DynamoDB, and Amazon Relational Database Service  
    * can also create custom metrics for services you develop, such as applications  
    * issues alarms when metrics reach a given threshold over a period of time.  
  * Amazon CloudWatch Logs
    * monitors and stores logs from Amazon EC2, AWS CloudTrail, and other sources.  
    * can use this capability to monitor any resource that generates logs without writing any code whatsoever.  
  * Amazon CloudWatch Events
    * produces a stream of events from changes to AWS environments, applies a rules engine, and delivers matching events to specified targets  



Monitoring is essential to understand systems behavior and to automate data-driven reactions. CloudWatch collects observations from runtime environments, in the form of metrics and logs, and makes those actionable through alarms, streams, and events.  



### Governance and Compliance

Although you have visibility into the current state of your AWS resource configurations using the AWS CLI and API calls, addressing these situations requires the ability to look at how those resources have changed over time. To address this need, AWS offers the AWS Config service. 

AWS Config enables you to assess, audit, and evaluate the configurations of your AWS resources. AWS Config automatically builds an inventory of your resources and tracks changes made to them.  

* AWS Config also provides a clear view of the resource change timeline, including changes in both the resource configurations and the associations of those resources to other AWS resources  
* When many different resources are changing frequently and automatically, automating compliance can become as important as automating the delivery pipeline. 
* To respond to changes in the environment, you can use AWS Config rules.  
  * With AWS Config rules, every change triggers an evaluation by the rules associated with the resources.  
  * AWS Config rules can quickly identify noncompliant resources and help with reporting and remediation.  
  * AWS Config rules also support the creation of custom rules using Lambda functions. These rules become part of the infrastructure code base, thus bringing the concept of Infrastructure as Code to the governance and compliance stages of the information resource lifecycle.  



AWS Config extends the concept of infrastructure code into the realm of governance and compliance. AWS Config can continuously record the configuration of resources while AWS Config rules allow for event-driven responses to changes in the configuration of tracked resources. You can use this capability to assist your organization with the monitoring of compliance controls.    



### Resource Optimization

In this stage, administrators review performance data and identify changes needed to optimize the environment around criteria such as security, performance, and cost management. It’s important for all application stakeholders to regularly evaluate the infrastructure to determine if it is being used optimally.  

i.e. 

* Are there provisioned resources that are under-utilised?
* Are there ways to reduce the charges associated with the operating environment?
* Are there any suggestions for improving performance of the provisioned resources?
* Are there any service limits that apply to the resources used in the environment and, if so, is the current usage of resources close to exceeding these limits?

These questions can all be answered with the help of **AWS Trusted Advisor**

* It helps you observe best practices by scanning your AWS resources and comparing their usage against AWS best practices in four categories: 
  * cost optimization
  * performance
  * security
  * fault tolerance  
* Trusted Advisor now integrates with Amazon CloudWatch Events. You can design a Lambda function to notify a Slack channel when the status of Trusted Advisor checks changes.  



You must continuously monitor your infrastructure to optimize the infrastructure resources with regard to performance, security, and cost. AWS Trusted Advisor provides the ability to use APIs to interrogate your AWS infrastructure for recommendations, thus extending Infrastructure as Code to the optimization phase of the information resource lifecycle. 