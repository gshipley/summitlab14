#**Lab 1: Overview of OpenShift Enterprise 2.0**

##**1.1 Assumptions**
This lab manual assumes that you are attending an instructor led training class and that you will be using this lab manual in conjunction with the lecture.  

I also assume that you have been granted access to two Red Hat Enterprise Linux servers with which to perform the exercises in this lab manual.  If you do not have access to your servers, please notify the instructor.

 A working knowledge of SSH, git, and yum, and familiarity with a Linux-based text editor are assumed.  If you do not have an understanding of any of these technologies, please let the instructor know.

##**1.2 What you can expect to learn from this training class**

At the conclusion of this training class, you should have a solid understanding of how to install and configure OpenShift Enterprise 2.0.  You should also feel comfortable in the usage of creating and deploying applications using the OpenShift Enterprise web console, OpenShift Enterprise administration console, command line tools, and managing the application lifecycle.

##**1.3 Overview of OpenShift Enterprise PaaS**

Platform as a Service is changing the way developers approach developing software. Developers typically use a local sandbox with their preferred application server and only deploy locally on that instance. Developers typically start JBoss locally using the startup.sh command and drop their .war or .ear file in the deployment directory and they are done.  Developers have a hard time understanding why deploying to the production infrastructure is such a time consuming process.

System Administrators understand the complexity of not only deploying the code, but procuring, provisioning and maintaining a production level system. They need to stay up to date on the latest security patches and errata, ensure the firewall is properly configured, maintain a consistent and reliable backup and restore plan, monitor the application and servers for CPU load, disk IO, HTTP requests, etc.

OpenShift Enterprise provides developers and IT organizations an auto-scaling cloud application platform for quickly deploying new applications on secure and scalable resources with minimal configuration and management headaches. This means increased developer productivity and a faster pace in which IT can support innovation.

This manual will walk you through the process of installing and configuring an OpenShift Enterprise 2.0 environment as part of this training class that you are attending.

##**1.4 Overview of IaaS**

The great thing about OpenShift Enterprise is that we are infrastructure agnostic. You can run OpenShift on bare metal, virtualized instances, or on public/private cloud instances. The only thing that is required is Red Hat Enterprise Linux as the underlying operating system. We require this in order to take advantage of SELinux and other enterprise features so that you can ensure your installation is stable and secure.

What does this mean? This means that in order to take advantage of OpenShift Enterprise, you can use any existing resources that you have in your hardware pool today. It doesn’t matter if your infrastructure is based on EC2, VMware, RHEV, Rackspace, OpenStack, CloudStack, or even bare metal as we run on top of any Red Hat Enterprise Linux operating system as long as the architecture is x86_64.

For this training class will be using OpenStack as our infrastructure as a service layer.

##**1.5 Using the *oo-install* tool**

In this training class, we are going to take advantage of the *oo-install* tool which automates the deployment and initial configuration of OpenShift Enterprise Platform.  However, for a deeper understanding of the internals of the platform, it is suggested that the student read through the official deployment guide for OpenShift Enterprise.


##**1.6 Electronic version of this document**

This lab manual contains many configuration items that will need to be performed on your broker and node hosts.  Manually typing in all of these values would be a tedious and error prone effort.  To alleviate the risk of errors, and to let you concentrate on learning the material instead of typing tedious configuration items, an electronic version of the document is available at the following URL:

	http://training.runcloudrun.com
	
In order to download all of the sample configuration files for the lab, enter the following command on your host:

	# wget -rnp --reject index.\* http://training.runcloudrun.com/labs/
	
**Lab 1 Complete!**

<!--BREAK-->
