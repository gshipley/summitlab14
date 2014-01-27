#**Lab 3: Installing OpenShift Enterprise 2.0 (Estimated time: XXX minutes)**

**Server used:**

* broker host

**Tools used:**

* oo-install

**Note:  For this lab, use the 209.x.x.x IP address when defining your hosts**

##**Overview of *oo-install***

The OpenShift team have developed a new interactive installer for the platform that simplifies the installation and configuration of the PaaS.  Using the *oo-install* tool is the ideal way to get an environment up and running quickly without having to worry about manually configuring all of the required services.  For a better understanding of how the tool works, it is suggested that the user view the source code of the installer to become familiar with the configuration involved in setting up the platform.  For this training class, once the installation of the PaaS has started, the instructor will go over the architecture of the PaaS so that you are familiar with all of the components and their purpose.

##**Installing the Ruby runtime**

The *oo-install* tool requires the use of a Ruby language runtime in order to execute.  However, Red Hat Enterprise Linux may not have this installed by default.  To check if your system has ruby installed, issue the following command:

**Note:** Execute the following on the broker host

	# ruby -v
	
If you have ruby installed you should see output similar to the following:

	ruby 1.8.7 (2011-06-30 patchlevel 352) [x86_64-linux]
	
If you get an error message stating that the ruby command can not be found, we will need to install the RPM package on the broker host.  In order to install the runtime, enter in the following command:

**Note:** Execute the following on the broker host

	# yum install ruby
	
Accept any certificates, if prompted, and ensure that the install was successful by running the *ruby -v* command again.


##**Download and Execute  *oo-install***

In order to use the *oo-install* tool, we will need to download and execute the command.  This can be performed in a single action from the command line:

**Note:** Execute the following on the broker host

	# sh <(curl -s https://install.openshift.com/ose-2.0/)
	
Once the download has completed, the installer will automatically start and you will be presented with the following information:

	OpenShift Installer
	----------------------------------------------------------------------
	
	Welcome to OpenShift.
	This installer will guide you through a basic system deployment, based
	on one of the scenarios below.
	
	Select from the following installation scenarios.
	You can also type '?' for Help or 'q' to Quit.":
	1. Install OpenShift Enterprise
	2. Add a Node to OpenShift Enterprise
	Type a selection and press <return>:
	
Since this is a new installation of OpenShift Enterprise 2.0, select option 1 and press the enter key.

The next section of the installation wizard will begin the configuration process for the OpenShift Enterprise Deployment.  The first question you will be asked is if you have an existing broker that is running:

	It looks like you are running oo-install for the first time on a new
	system. The installer will guide you through the process of defining
	your OpenShift deployment.
	
	First things first: do you already have a running Broker? (y/n/q)

Since this is a new installation, enter in *n* and hit enter.  The next step is to gather the domain name that you would like to use your applications that will be hosted on the OpenShift Enterprise Platform.  The default is example.com but it makes more sense to from an architecture view point to separate these out to their own domain.  

	Okay. We will gather information to install a Broker in a moment.
	Before we do that, we need to collect some information about your
	OpenShift DNS configuration.
	
	What domain do you want to use for applications that are hosted by
	this OpenShift deployment? |example.com|
	
Enter in *apps.example.com* and hit the enter key.  At this point, the installation program will ask you if you want to register the DNS entries for the hosts that we will be creating.  Since we are going to be using the BIND server that ships with OpenShift Enterprise, we can select *y* here.

	Do you want to register DNS entries for your OpenShift hosts with the
	same OpenShift DNS service that will be managing DNS records for the
	hosted applications? (y/n/q)

The next step of the installation is to configure the domain that we want to use for our OpenShift Enterprise Hosts.  It is good practice to put your hosts (broker and nodes) on a separate domain.

	What domain do you want to use for the OpenShift hosts?
	
Enter in *hosts.example.com* and press the enter key.

##**Adding a Broker host**

Now that we have our DNS configuration set for our hosts, we can continue with the installation by specifying the fully qualified domain name for our broker.

	----------------------------------------------------------------------
	Broker Configuration
	----------------------------------------------------------------------
	
	Okay. I'm going to need you to tell me about the host where you want
	to install the Broker.
	
	Hostname (the FQDN that other OpenShift hosts will use to connect to
	the host that you are describing):
	
Enter in *broker.hosts.example.com* and press the enter key.  

	Hostname / IP address for SSH access to broker.hosts.example.com from
	the host where you are running oo-install. You can say 'localhost' if
	you are running oo-install from the system that you are describing:
	|broker.hosts.example.com|

The next step of the process is to tell the installer the IP address of our broker.hosts.example.com server.  For this question, use the 209.x.x.x address that was provided to you by your instructor.  Please ensure that you are using the IP address for the broker host here.

You will then be prompted for the username to connect to the broker host.  Use the default of root by pressing enter and you should see the following information on the screen:

	Validating root@209.x.x.x... looks good.
	
	Detected IP address 192.168.59.X at interface eth0 for this host.
	Do you want Nodes to use this IP information to reach this host?
	(y/n/q/?)
	
If you were not able to connect to your broker host with the provided IP address, verify that you copied over the ssh key as mentioned in a previous lab.  If you are still having problems with this step, please alert your instructor.

If you were able to successfully connect by seeing the above message, select *y* to use this ip address for the node to connect to the broker host.

##**Adding a Node host**

We can now begin the process of installing and configuring the node host.  Since we don't already have a node host configured, select *n* at the following prompt:

	----------------------------------------------------------------------
	Node Configuration
	----------------------------------------------------------------------
	Do you already have a running Node? (y/n/q)
	
Once you select *n*, the installer will ask you if you want to add the *Node* role to the existing broker host that we previously configured.  This is useful if you want to spin up an OpenShift Enterprise Environment and colocate both the broker, node, and all gears on a single operating system instance.  However, for this lab we will be installing a more production like system by having our broker and node hosts on their own hardware.  

	Okay. I'm going to need you to tell me about the host where you want
	to install the Node.
	
	You have already described the following host system(s):
	* broker.hosts.example.com (Broker)
	
	Do you want to assign the Node role to broker.hosts.example.com?
	(y/n/q/?)
	
Select *n* and press enter to allow us to enter in the information for our node host.

	Okay, please provide information about the Node host.
	
	Hostname (the FQDN that other OpenShift hosts will use to connect to
	the host that you are describing):
	
To follow the pattern that we set earlier in this lab by having all of our hosts located on their own domain, enter in *node1.hosts.example.com* and press enter.  You can then enter in the 192.x.x.x address of your node host that was provided to your by the instructor of this class.

	Hostname / IP address for SSH access to node1.hosts.example.com from
	the host where you are running oo-install. You can say 'localhost' if
	you are running oo-install from the system that you are describing:
	|node1.hosts.example.com|
	
Once you enter in the IP address of your node host, ensuring that it is the correct 209.x.x.x address, provide the authentication details just as you did for the broker host.  If the installer was able to connect via SSH to your node host, you will be presented with the following information:

	Username for SSH access to 192.168.59.x: |root|
	
	Validating root@192.168.59.x... looks good.

	Detected IP address 192.168.59.x at interface eth0 for this host.
	Do you want to use this as the public IP information for this Node?
	(y/n/q/?)
	
Just as we did with the broker host, select *y* to use the IP address as the public information.

After completing that step, you will be presented with an overview of the installation information provided.  Ensure that the details of the configuration are correct.

	Here are the details of your current deployment.
	
	Note: ActiveMQ, MongoDB and named will all be installed on the Broker.
	For more flexibility, rerun the installer in advanced mode (-a).
	
	DNS Settings
	  * App Domain: example.com
	  * Register OpenShift components with OpenShift DNS? Yes
	  * Component Domain: hosts.example.com
	
	Role Assignments
	+--------+--------------------------+
	| Broker | broker.hosts.example.com |
	| Node   | node1.hosts.example.com  |
	+--------+--------------------------+
	
	Host Information
	+----------------+--------------------------+
	| Host           | broker.hosts.example.com |
	| Roles          | Broker                   |
	| SSH Host       | 192.168.59.X             |
	| User           | root                     |
	| IP Addr        | 192.168.59.X             |
	| Install Status | new                      |
	+----------------+--------------------------+
	+----------------+-------------------------+
	| Host           | node1.hosts.example.com |
	| Roles          | Node                    |
	| SSH Host       | 192.168.59.X            |
	| User           | root                    |
	| IP Addr        | 192.168.59.X            |
	| IP Interface   | eth0                    |
	| Install Status | new                     |
	+----------------+-------------------------+
	
	Do you want to change the deployment info? (y/n/q/?)
	
If everything looks correct, enter *n* to move the subscription management section of the installation.
	
##**Performing the installation**

Now that we have both the broker and node host installation configuration complete, we can move on to the next step of the deployment configuration: subscription management.

If you performed the subscription steps correctly in Lab 2, you will not need to enter in any subscription information when presented with this information:

	Here is subscription configuration that the installer will use for
	this deployment.
	+---------+-------+
	| Setting | Value |
	+---------+-------+
	| type    | none  |
	+---------+-------+
	
	Do you want to make any changes to the subscription info in the
	configuration file? (y/n/q/?)
	
Select *n* and press enter and then select to not set any temporary subscription settings to begin the installation.

The install will now run and may take a while depending on the speed of the connection at your location and the number of RPM packages that need to be installed.  During this time, the instructor will lecture about the architecture of OpenShift Enterprise.

If you want to watch the progress of the install, you can tail the log file(s) on the broker and the host:

	$ tail -f /tmp/open*.log
	
	
**Lab 3 Complete!**

<!--BREAK-->