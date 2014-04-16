#**Lab 2: Installing OpenShift Enterprise 2.0**

**Servers used:**

* broker host
* node host

**Tools used:**

* *openshift.sh*

##**Overview of *openshift.sh***

The OpenShift team has developed an installation script for the platform that simplifies the installation and configuration of the PaaS.  The *openshift.sh* script is a flexible tool to get an environment up and running quickly, without having to worry about manually configuring all of the required services.  For a better understanding of how the tool works, it is suggested that you view the source code of *openshift.sh* to become familiar with the configuration involved in setting up the platform.  For this training class, once the installation of the PaaS has started, the instructor will go over the architecture of the PaaS to get you familiar with all of the components and their respective purposes.

A copy of *openshift.sh* has already been loaded on each system provided to you.  This script is also available in the [enterprise-2.0 branch of the openshift-extras Github repository](https://github.com/openshift/openshift-extras/tree/enterprise-2.0).  In that repository, you can find a [kickstart version](https://github.com/openshift/openshift-extras/blob/enterprise-2.0/enterprise/install-scripts/openshift.ks) of the script as well as other versions.  The script we will be using is the [generic openshift.sh](https://github.com/openshift/openshift-extras/blob/enterprise-2.0/enterprise/install-scripts/generic/openshift.sh) script.

##**Installing and configuring the OpenShift broker host**

The *openshift.sh* script takes arguments, in the form of environment variables or command-line arguments, to specify settings for the script.  All of the recognized settings are documented extensively in comments at the top of the script.  For our purposes, we will want to specify the following settings when we use this script to install the broker host:

* *install_components=broker,named,activemq,datastore*
* *domain=apps.example.com*
* *hosts_domain=hosts.example.com*
* *broker_hostname=broker.hosts.example.com*
* *named_entries=broker:{host1 IP address},activemq:{host1 IP address},datastore:{host1 IP address},node:{host2 IP address}*

Let's go over each of these options in more detail.

### *install_components* setting ###

The *install_components* setting specifies which components the script will install and configure.  In this training session, we will install the OpenShift broker component and supporting services on which OpenShift depends on one host, and we will install the OpenShift node component on a second host.

In more complex installations, you will want to install each component on a separate host (in fact, you will want to install most components on several hosts each for redundancy).  For testing or POCs, you may want to install all components (including both the OpenShift broker and node) on a single host, which is the default if you do not specify a setting for *install_components*.

### *domain* and *host_domain* settings ###

The *domain* setting specifies the domain name that you would like to use for your applications that will be hosted on the OpenShift Enterprise platform.  The default is example.com, but it makes more sense from an architecture view point to separate these out to their own domain, which we will do in this lab, using the apps.example.com domain.

The *hosts_domain* setting specifies the domain name that you would like the OpenShift infrastructure hosts to use, which includes the OpenShift broker and node hosts.  This domain also includes hosts running supporting services, such as ActiveMQ and MongoDB, although in our case, we are running these services on the same host as the OpenShift broker component.

While it is not required to do so, it is good practice to put your infrastructure hosts (broker and nodes) under a separate domain from applications, and we will follow this practice in this lab, using the hosts.example.com domain.

### *broker_hostname* setting ###

The *broker_hostname* setting specifies the fully-qualified hostname that the installation script will configure for the OpenShift broker host.  In a more complex configuration with redundant brokers, you will want to use this setting to specify a unique hostname for each host that you install (e.g., *broker_hostname=broker01.hosts.example.com* on the first OpenShift broker host, *broker_hostname=broker02.hosts.example.com* on the second OpenShift broker host, and so on).  For this training session, we will only be installing one broker host.

### *named_entries* setting ###

The *named_entries* setting is used when the script installs the nameserver to add DNS records for the various hosts and services we will be installing.

Because we are telling *openshift.sh* to install a nameserver alongside the OpenShift broker, the script will automatically configure the host to use itself as its own nameserver.  If we were not installing the nameserver on the broker host, we would need to tell *openshift.sh* what nameserver to use using an additional setting (we will use this additional setting, the *named_ip_addr*, when we configure our second host as an OpenShift node host).

### Executing *openshift.sh* ###

Let's go ahead and execute the command to run installation script.

First, make sure you are connected to the *lab7-broker* virtual machine that has been provided to you in this lab.  Open a terminal window and connect to the VM using SSH:

	ssh root@172.16.1.2

Log in with the password 'redhat'.  Once you are connected to the correct host, set the *host1* and *host2* environment variables, for our own use:

**Note:** Execute the following command on the broker host.

	host1=172.16.1.2; host2=172.16.1.3

Next, for *openshift.sh*, set the following environment variables:

**Note:** Perform the following command on the broker host.

	export CONF_INSTALL_COMPONENTS=broker,named,activemq,datastore
	export CONF_DOMAIN=apps.example.com
	export CONF_HOSTS_DOMAIN=hosts.example.com
	export CONF_BROKER_HOSTNAME=broker.hosts.example.com
	export CONF_NAMED_ENTRIES=broker:$host1,activemq:$host1,datastore:$host1,node:$host2

While we could use the corresponding command-line arguments to specify these settings, we will use environment variables to force ourselves to be careful.  After setting the above variables, run the following command and verify that the variables have all been set as described above and have IP addresses substituted appropriately:

	env | grep CONF_

**Note:** Verify that the settings are correct.

Now let's execute *openshift.sh*.  Note that we will use the *tee* command to make an installation log.

**Note:** Perform the following command on the broker host.

	sh openshift.sh |& tee broker-install.log

While the installation script runs on the OpenShift broker host, open a console for your node host, and continue on to the next section to begin the installation and configuration of this host.

##**Installing and configuring the OpenShift node host**

To install and configure the OpenShift node host, we will want to specify the following settings to *openshift.sh*:

* *install_components=node*
* *cartridges=all,-jboss,-jenkins,-postgres,-diy*
* *domain=apps.example.com*
* *hosts_domain=hosts.example.com*
* *named_ip_addr={host1 IP address}*
* *node_hostname=node.hosts.example.com*

Following is an explanation for each of these arguments.

### *install_components* and *cartridges* settings ###

We will specify the *install_components* setting for this host to configure it as an OpenShift node host.  For node hosts, it is possible to specify the list of *cartridges* that *openshift.sh* will install on the host.  Cartridges provide functionality to application developers and will be explained in the lecture.  For now, we want to specify a limited set of cartridges to install in order to reduce the installation time in this lab.

### *domain* and *host_domain* settings ###

We described the *domain* and *hosts_domain* settings earlier in this lab while installing and configuring the OpenShift broker host.

### *named_ip_addr* setting ###

For the *named_ip_addr* setting, we will use the address for *host1*. We use the *named_ip_addr* setting to configure name resolution on the OpenShift node host to use our own nameserver.

### *node_hostname* setting ###

The *node_hostname* setting specifies the fully-qualified hostname that the installation script will configure for the OpenShift node host.

### Executing *openshift.sh* ###

First, make sure you are connected to the *lab7-node* virtual machine.  Open a terminal window and connect to the VM using SSH:

	ssh root@172.16.1.3

Log in with the password 'redhat'.  Before we execute the command to run installation script, let's set the *host1* and *host2* environment variables as we did on the broker host:

**Note:** Perform the following command on the node host.

	host1=172.16.1.2; host2=172.16.1.3

Next, set the following environment variables:

	export CONF_INSTALL_COMPONENTS=node
	export CONF_CARTRIDGES=all,-jboss,-jenkins,-postgres,-diy
	export CONF_DOMAIN=apps.example.com
	export CONF_HOSTS_DOMAIN=hosts.example.com
	export CONF_NAMED_IP_ADDR=$host1
	export CONF_NODE_HOSTNAME=node.hosts.example.com

Run the following command and verify that the settings are correct and have the appropriate IP addresses substituted:

	env | grep CONF_

**Note:** Verify that the settings are correct.

Now launch the installation script:

	sh openshift.sh |& tee node-install.log

Allow the installation script to run.  During this time, the instructor will lecture about the architecture of OpenShift Enterprise.

**Lab 2 Complete!**

<!--BREAK-->
