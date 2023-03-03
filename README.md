# Working with Elastic Load Balancing
This lab introduces the concept of Elastic Load Balancing(ELB). In this lab you will use ElasticLoad Balancing to load balance traffic across multiple Amazon Elastic Compute Cloud(EC2)instances in a single Availability Zone. You will deploy a simple application onmultiple Amazon EC2 instances and observe load balancing by viewing the application inyour browser.

First , you will launch a pair of instances , bootstrap them to install web servers andcontent , and then access the instances independently using Amazon EC2 DNS recordsNext , you will set up Elastic Load Balancing(ELB) , add your instances to the load balancer , andthen access the DNS record again to watch your requests load balance between serversFinally you will view Elastic Load Balancing(ELB) metrics in Amazon Cloudwatch

The following diagram provides a high-level overview of the architecture you wiimplement in this exercise

# Topics covered

This lab will take you through:

  * Launching a multiple server web farm on Amazon EC2
  
  * Using bootstrapping techniques to configure Linux instances with Apache , PHP and asimple PHP application downloaded from Amazon Simple Storage Service ( S3 )

  * Creating and configuring a load balancer that will sit in front of your Amazon EC2 webserver Instances
  
  * Viewing Amazon Cloud Watch metrics for the load balance
  
  # Technical knowledge prerequisites
  
  To successfully complete this lab , you should be familiar with the AWS Management Console
  
  
Elastic Load Balancing(ELB)

Elastic Load Balancing(ELB) automatically distributes incoming application traffic acrossmultiple Amazon EC2 instances . It enables you to achieve greater levels of fault tolerancein your applications , seamlessly providing the required amount of load balancing capacityneeded to distribute application traffi

Achieve higher levels of fault tolerance for your applications by using Elastic LoadBalancing to automatically route traffic across multiple instances and multiple AvailabilityZones . Elastic Load Balancing ensures that only healthy Amazon EC2 instances receivetraffic by detecting unhealthy instances and rerouting traffic across the remaining healthyinstances . If all of your Amazon EC2 instances in one Availability Zone are unhealthy , andyou have set up Amazon EC2 instances in multiple Availability Zones , Elastic LoadBalancing will route traffic to your healthy Amazon EC2 instances in those other zones

Elastic Load Balancing automatically scales its request-handling capacity to meet thedemands of application traffic . Additionally , Elastic Load Balancing offers integration withAuto Scaling to ensure that you have back-end capacity to meet varying levels of trafficevels without requiring manual intervention

Elastic Load Balancing works with Amazon Virtual Private Cloud ( VPC ) to provide robustnetworking and security features . You can create an internal ( non-internet facing ) loadbalancer to route traffic using private IP addresses within your virtual network . You canmplement a multi-tiered architecture using internal and Internet-facing load balancers toroute traffic between application tiers . With this multi-tier architecture , your applicationinfrastructure can use private IP addresses and security groups , allowing you to exposeonly the Internet-facing tier with public IP addresses

Elastic Load Balancing provides integrated certificate management and SSL decryption ,allowing you to centrally manage the SSL settings of the load balancer and offload CPUintensive work from your instances

his lab guide explains basic concepts of Elastic Load Balancing in a step-by-step fashionHowever , it can only give a brief overview of Elastic Load Balancing concepts . For further information, see [http://aws.amazon.com/elasticloadbalancing/](https://aws.amazon.com/elasticloadbalancing/)

# Task 1 : Launch Web Servers

In this task , you will launch two Amazon Linux EC2 instances , with an Apache PHP webserver and basic application installed on initialization . You will also demonstrate a simpleexample of bootstrapping instances using the Amazon EC2 metadata service

Amazon Machine Images ( Amls ) and instances


Amazon EC2 provides templates known as Amazon Machine Images ( AMIS ) that containa software configuration ( for example , an operating system , an application server , andapplications ) . You use these templates to launch an instance , which is a copy of the AMrunning as a virtual server In the cloud


You can launch different types of instances from a single AML . An instance typeessentially determines the hardware capabilities of the virtual host computer for yourinstance . Each instance type offers different compute and memory capabilities . Select aninstance type based on the amount of memory and computing power that you need for theapplication or software that you plan to run on the instance . You can launch multipleinstances from an AMI

Your instance keeps running until you stop or terminate it , or until it fails . If an instanceails , you can launch a new one from the AMI

When you create an instance , you will be asked to select an instance type . The instancetype you choose determines how much throughput and processing cycles are available toyour Instance


