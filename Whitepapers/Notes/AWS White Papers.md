# AWS Security Best Practices

Printed copy, comments on the paper



# Infrastructure as Code [2017]

DevOps is the combination of cultural philosophies, practices, and tools that increases your organisation’s ability to deliver applications and services at high velocity. 



##### Intro to Infrastructure as Code

Cloud computing takes advantage of virtualisation to enable the on-demand provisioning of compute, network, and storage resources that constitute technology infrastructures. 

The manual processes have certain disadvantages, including: 

* Higher cost as a result of unnecessary human capital
* Human error means inconsistency and thus deviation rom 
* Lack of agility
* Difficulty in attaining and maintaining compliance to corporate or industry standards due to the absence of repeatable processes

Infrastructure as Code addresses these deficiencies by bringing automation to the provisioning process. Rather than relying on manually performed steps, both administrators and developers can instantiate infrastructure using configuration files 	

Infrastructure as Code eliminates configuration drift through automation, thereby increasing the speed and agility of infrastructure deployments. 



##### Infrastructure Resource Lifecycle

![Screen Shot 2018-08-26 at 17.30.02](/Users/brandonpickup/Desktop/AWS/Whitepapers/Diagrams/Screen Shot 2018-08-26 at 17.30.02.png)



Each stage involves procedures that can leverage code. This extends the benefits of Infrastructure as Code from its traditional role in provisioning to the entire resource lifecycle. Every lifecycle then benefits from the consistency and repeatability that Infrastructure as Code offers. 



#### Resource Provisioning

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



#### Configuration Management



#### Monitoring and Peformance

#### Governance and Compliance

#### Resource Optimization

