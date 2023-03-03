# Working with Elastic Load Balancing(EC2)
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

3. On the `Services menu`, click `EC2`

4. In the left navigation pane, click `Instances`

5. Click `Launch Instances`

6. In the `Name and tags` section

 * Name: `Mylbinstances`


This name, more correctly known as a tag, will appear in the console when thestance launches. It makes it easy to keep track of running machines in acomplex environment. Use a name that you can easily recognize and remembe

`
7. In `Application and OS Images ( Amazon Machine Image )` , Quick Start subsection click `Amazon Linux`

8. Latest `Amazon Linux 2 AMI`` shall be selected automatically


The t2.micro instance type , which is the lowest-cost option , should beautomatically selected

9. In the `Key pair ( login )` section

  * Select `Proceed without a key pair ( Not recommended )`

10. In the `Network settings` section , click `Edit`

  * Vpc-required: Lab VPC
  
  * Firewall(security groups): `Create security group`
  
  * Security group name-required: `Mylbinstances`
  
  * Description - required: `Mylbinstances`

A security group acts as a firewall that controls the traffic allowed into agroup of instances When you launch an Amazon EC2 instance , you carassign it to one or more security groups . For each security group , you addrules that govern the allowed inbound traffic to instances in the groupother inbound traffic is discarded . You can modify rules for a security groupat any time . The new rules are automatically enforced for all existing andfuture instances in the group

By default , AWS creates a rule that allows Secure Shell ( SSH ) access fromany IP address . It is highly recommended that you restrict terminal access tothe ranges of IP addresses ( e . g , IPS assigned to machines within yourcompany that have a legitimate business need to administer your AmazonC2 instance

11. Click `Remove` to remove the SSH rule

12. Click Add security group rule then configure:

  * Type: `HTTPS`
  * Source type: `Anywhere`

This will add adefault handler for HTTP that will allow requests from anywhere on the Internet . Since you want this web server to be accessible tothe general public

13. In the `Configure storage` section, you will use the default storage devicecontiguration

14. In the `Advanced Details` section , enter the following into User data

```
#!/bin/sh
yum -y install httpd php
chkconfig httpd on
systemctl start httpd.service
cd /var/www/html
wget https://us-west-2-aws-training.s3.amazonaws.com/courses/spl-03/v4.3.16.prod-c0f716ea/scripts/examplefiles-elb.zip
unzip examplefiles-elb.zip
```

This will allow you to bootstrap your instance . It will install Apache and PHPand sample code ( PHP scripts ) needed for this lab when the instance iscreated and launched . User data provides a mechanism to pass data or ascript to the Amazon metadata service , which instances can access at launchtime

Tip If you type this text instead of copying it , press SHIFT + ENTER to createnew lines in the text box

15. In the Summary section:

  * Number of instances: `2`

16. Review your choices , and then click `Launch instance`

You may see a warning on this screen that " Your security group . is opento the world . This is a result of not restricting SSH access to your machine ,as described earlier . For the purposes of this lab only , you may ignore thiswarning

Please do not launch your instance with a key pair

A status page notifies you that your instances are launching

17. Click `View all instances`

18. Wait for your instances to display:

  * Instance State:  running
  * Status Checks:  2/2checks passed
  
 This indicates that your instance is now fully available
 
This may take a few minutes . You can refresh the status of your instancesby clicking the refresh icon


# Task 2 : Connect to Each Web Server

In this task , you will:

 * Retrieve the public DNS entries for both of your EC2 instances
 
 * Point your browser at each DNS entry to access your instances web serve

All Amazon EC2 instances are assigned two IP addresses at launch : a private/ P address ( RFC 1918 ) and a public IP address that are directly mapped toeach other through Network Address Translation ( NAT ) . Private IP addressesare only reachable from within the Amazon EC2 network Public addressesare reachable from the Internet

Amazon EC2 also provides an interna / DNS name and a pub / ic DNS name thatmap to the private and public IP addresses , respectively . The internal DNSname can only be resolved within Amazon EC2 . The public DNS nameresolves to the public IP address outside the Amazon EC2 network and to theprivate IP address within the Amazon EC2 network

19. Select your first Amazon EC2 instance

20. Copy the `Public DNS (IPV4)` value to your Clipboard

looksomethinglikeec2-54-84-236-205.compute-1.amazonaws.com

21. Open a new browser window , paste the Public DNS value into the addressbar , and press ENTER Your browser will display a screen like this


This is the web page returned by the PHP script that was installed when thenstance was started . It is a simple script that interrogates the metadataservice on each machine and returns the instance ID and the name of theAvailability Zone in which the instance is running


22. Repeat the previous two steps for your second instance

Notice that each machine displays a different instance ID . This will help youidentify which instance is processing your request when you put a loadbalancer in front of them

If you see an error instead of the instance d and availability zone when youaccess the instances from the browser , try again after a couple of minutesits possible that the bootstrapping script is still running and has not yetcompleted installing and starting the web server and php applicationerrors persist , verify that you entered the bootstrap script correctly when youlaunched your instances and that the security group has port 80 ope



# Task 3 : Create a Load Balancer


You now have two web servers . Now you need a load balancer in front ofthese servers to give your users a single location for accessing both and tobalance user requests across them . In this task , you will be create a simpleITTP application load balance


23. In the `AWS Management Console` , on the left navigation pane click `Load Balancers`

24. Click Create Load Balancer

25. Below Application Load Balancer , click Create then configure

26. Below Basic confiquration:

  * load balancer паmе : `ELB`
  
  
27. below network mapping:

  VPC: Lab VPC
  Select all of the availability zones under Mapping
  
Below Security groups:

  * Delete `default`
  * Select `Mylbinstances`


28. Below Security groups:

  * Delete E default
  * Select Mylbinstances

29. Below listeners and routing : click on create target group

30. In the next tab , configure following details
  
   * Target group name: my target
   
   
30. Expand  Advanced health check settings then configure

 * Healthy threshold : 3
 * Click Next
 
The ELB will periodically test the ping path on each of your web serviceIstancestodeterminehealtha200HTTPresponsecodeindicatesahealthystatus , and any other response code indicates an unhealthy status . If anIstance is unhealthy and continues in that state for a successive number ofchecks ( unhealthy threshold ) , the load balancer will remove it from serviceuntil it recovers\


In this configuration , making the ping path just a slash ( will return thedefault page-the Php-generated page seen earlier


The healthy threshold is the number of successful checks the load balanceexpects to see in a row before bringing an instance into service behind theload balancer . The lower value will speed things up for this exercise

31. Select both of your instances

32. Click on Include as pending below

33. Click Create target group

34. Come back to the original load balancer tab

35. Click on the refresh button within Listeners and routing

36. Select my Target from dropdown menu

37. Click Create load balancer

38. Click View load balancer

It will take a couple of minutes to start up the load balancer , attach your webservers , and pass the health checks

39. Wait for the State to display Active

40. Copy the DNS name to your clipboard

41. Open a new browser tab and then：

  *  Paste the DNS name
  
  * Press Enter
  
42. Refresh your browser a few times

You will notice that the Amazon EC2 Instance IDS change . This means thathe repeated responses are coming back through your two different webservers

Load balancers can span Availability Zones , and they also scale elastically asneeded to handle demand . Therefore , you should always access a loadbalancer by DNS hostname , and not by IP address . A load balancer may havemultiple IP addresses associated with its DNS hostname

43. In the `AWS Management Console` , in the left navigation pane , click `Targe tGroups`

44. myTarget should be selected

45. Click the Targets tab

Notice that both of your instances are healthy


# Task 4 : View Elastic Load BalancingCloud Watch Metrics

In this task , you will use Amazon Cloud Watch to monitor your ELB Elasticoad Balancing automatically reports load balancer metrics to Cloudwatch


Amazon Cloudwatch provides monitoring for AWS cloud resources and theapplications customers run on AWS Developers and system administratorscan use it to collect and track metrics , gain insight , and react immediately tokeep their applications and businesses running smoothly . Cloud Watchmonitors AWS resources such as Amazon EC2 and Amazon RelationaDatabase Service ( RDS ) DB instances and can also monitor custom metricsgenerated by your applications and services With Cloudwatch , you gainsystem-wide visibility into resource use , application performance , andoperational health


Amazon Cloud Watch provides a reliable , scalable , and flexible monitoringsolution that you can start using within minutes . You no longer need to set up ,manage , or scale your own monitoring systems and infrastructure . UsingCloudwatch , you can easily monitor as much or as little metric data as youneed Cloudwatch lets you programmatically retrieve your monitoring data ,view graphs , and set alarms to help you troubleshoot , spot trends , and takeautomated action based on the state of your cloud environment


45. In the `AWS Management Console` . on the `Services` menu click `Cloud Watch`

46. In the left navigation pane , click `Metrics`

47. Click on All metrics

48. Click Applicationelb

49. Explore the metrics that are beina reported to CloudWatch

50. It may take upto 5 minutes for the metrics to be populated

Load balancing metrics include latency , request count , and healthy andunhealthy host counts . Metrics are reported as they are encountered and calake several minutes to show up in Cloudwatch


# Conclusion

  * Launched a multiple server web farm on Amazon EC2
  
  * Used bootstrapping techniques to configure Linux instances with Apache PHP and a simple PHP application
  
  * Created and configured a load balancer that sits in front of your Amazon EC2 web server instances
  
  * Viewed Amazon Cloudwatch metrics for the load balancer


# Additional Resources

  * [Eastic Load Balancing and Elastic Load Balancing pricing](https://aws.amazon.com/elasticloadbalancing/)
  * [Working with Elastic Load Balancing](https://amazon.qwiklabs.com/focuses/57391?catalog_rank=%7B%22rank%22%3A1%2C%22num_filters%22%3A0%2C%22has_search%22%3Atrue%7D&parent=catalog&search_id=22600880)
  * [Introduction to Amazon API Gateway](https://amazon.qwiklabs.com/focuses/53694?parent=catalog)

