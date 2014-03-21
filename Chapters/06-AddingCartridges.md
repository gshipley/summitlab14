
#**Lab 6: Adding cartridges**

**Server used:**

* node host
* broker host

**Tools used:**

* yum
* oo-admin-yum-validator

There are several steps to installing a cartridge on OpenShift Enterprise. First, as one might expect, it is necessary to install the RPM package for the cartridge on all of your OpenShift node hosts so that the cartridge's files will be in the filesystem.

The OpenShift Enterprise node runtime maintains a *cartridge repository*. After installing the RPM for a cartridge, it is necessary to register the cartridge with the node runtime, which will copy the cartridge's files and metadata into the cartridge repository.

The OpenShift Enterprise broker gets the list of available cartridges by querying the OpenShift nodes via MCollective. The first time the broker retrieves the list of available cartridges, the broker caches the response so that subsequent requests for this information will be processed more quickly. If you install a new cartridge, it is unavailable to users until the broker's cache is cleared and MCollective retrieves a new list of cartridges. 

This lab will focus on installing cartridges to allow OpenShift Enterprise to create JBoss gears.

##**Configuring Red Hat Network Channels**

When we installed our OpenShift node host in an earlier lab, we told the *openshift.sh* installation script to install the node component with an explicit list of cartridges.  As part of the installation process, *openshift.sh* ran the *oo-admin-yum-validator* tool to adjust and validate the Yum channel configuration.

Because OpenShift Enterprise is a layered product that incorporates many technologies, it downloads packages from many channels, which must be enabled.  Moreover, these channels sometimes have conflicting packages that can cause dependency resolution errors during installation or other problems at runtime, and so it is necessary to set appropriate priorities and sometimes specific package exclusions.  The OpenShift Enterprise team wrote the *oo-admin-yum-validator* tool to perform all this configuration automatically.

Recall that we installed an OpenShift node without JBoss cartridges, *oo-admin-yum-validator* thus configured the host with only the standard Red Hat Enterprise Linux and OpenShift Enterprise Node channels.  To install the JBoss cartridge RPMs and the JBoss RPMs packages on which the cartridges depend, we need several additional channels enabled.

Run the following command to enable these additional channels.

**Note:  Run the following command on the node host.**

	# oo-admin-yum-validator -o 2.0 --fix-all --role node-eap

The above command tells *oo-admin-yum-validator* that we the Yum channels on the current host configured appropriately for OpenShift Enterprise 2.0 running in the "node-eap" role, which means an OpenShift node host with the JBossEAP cartridge.  (The "node" and "node-eap" roles both include non-JBoss packages as well, so you do not lose access to other technologies when you enable the "node-eap" role for JBoss technologies.) The above command should make the necessary configuration adjustments for you.  After it runs, run it again without the *--fix-all* option in order to validate the configuration:

**Note:  Run the following command on the node host.**

	# oo-admin-yum-validator -o 2.0 --role node-eap

If the above command detects no problems, you should now be able to install the needed RPM packages.

##**Listing available cartridges for your subscription**

For a complete list of all cartridges that you are entitled to install,  you can perform a search using the *yum* command that will output all OpenShift Enterprise cartridges.

**Note:  Run the following command on the node host.**

	# yum search origin-cartridge

You should see the following cartridges available to install:

openshift-origin-cartridge-cron.noarch : Embedded cron support for OpenShift
openshift-origin-cartridge-diy.noarch : DIY cartridge
openshift-origin-cartridge-haproxy.noarch : Provides HA Proxy
openshift-origin-cartridge-jbosseap.noarch : Provides JBossEAP6.0 support
openshift-origin-cartridge-jbossews.noarch : Provides JBossEWS2.0 support
openshift-origin-cartridge-jenkins.noarch : Provides jenkins-1.x support
openshift-origin-cartridge-jenkins-client.noarch : Embedded jenkins client support for OpenShift
openshift-origin-cartridge-mysql.noarch : Provides embedded mysql support
openshift-origin-cartridge-nodejs.noarch : Provides Node.js support
openshift-origin-cartridge-perl.noarch : Perl cartridge
openshift-origin-cartridge-php.noarch : Php cartridge
openshift-origin-cartridge-postgresql.noarch : Provides embedded PostgreSQL support
openshift-origin-cartridge-python.noarch : Python cartridge
openshift-origin-cartridge-ruby.noarch : Ruby cartridge

##**Installing JBoss support**

In order to enable consumers of the PaaS to create JBoss gears, we will need to install all of the necessary cartridges for the application server and supporting build systems.  Perform the following command to install the required cartridges:

**Note:  Execute the following on the node host.**

	# yum install openshift-origin-cartridge-jbosseap openshift-origin-cartridge-jbossews openshift-origin-cartridge-postgresql
	
The above command will allow users to create JBoss EAP and JBoss EWS gears.  We also installed support for the Jenkins continuous integration environment which we will cover in a later lab.  At the time of this writing, the above command will download and install an additional 285 packages on your node host.

**Note:** Depending on your connection and speed of your node host, this installation make take several minutes.  

##**Updating the cartridge repository**

Once the RPM packages for the JBoss and Jenkins cartridges are installed, we need to restart the OpenShift node runtime so that it will detect these cartridges and install them into its cartridge repository. The runtime is implemented as an MCollective agent. Run the following command to restart it:

**Note:  Execute the following on the node host.**

	# service ruby193-mcollective restart

You can verify that the cartridges are in the cartridge repository with the *oo-admin-cartridge* command.

**Note:  Execute the following on the node host.**

	# oo-admin-cartridge --list
	(redhat, python, 2.6, 0.0.8)
	(redhat, python, 2.7, 0.0.8)
	(redhat, ruby, 1.8, 0.0.10)
	(redhat, ruby, 1.9, 0.0.10)
	(redhat, nodejs, 0.10, 0.0.8)
	(redhat, diy, 0.1, 0.0.5)
	(redhat, jbossews, 1.0, 0.0.9)
	(redhat, jbossews, 2.0, 0.0.9)
	(redhat, cron, 1.4, 0.0.8)
	(redhat, php, 5.3, 0.0.8)
	(redhat, postgresql, 8.4, 0.3.6)
	(redhat, postgresql, 9.2, 0.3.6)
	(redhat, mysql, 5.1, 0.2.6)
	(redhat, jbosseap, 6, 0.0.8)
	(redhat, perl, 5.10, 0.0.7)
	(redhat, haproxy, 1.4, 0.0.9)

Verify that you see the "jbossews" (1.0 and 2.0) and "jbosseap" cartridges in the output when you run the command.

##**Clearing the broker application cache**

At this point, you will notice that if you try to create a JBoss based application via the management console, the application type is not available.  This is because the broker host creates a cache of available cartridges to increase performance.  After adding a new cartridge, you need to clear this cache in order for the new cartridge to be available to users.

Caching is performed in multiple components:

* Each node maintains a database of facts about itself, including a list of installed cartridges.
* Using MCollective, a broker queries a node's facts database for the list of cartridges and caches the node's response.
* Using the broker's REST API, the management console queries the broker for the list of cartridges and caches the broker's response.

The cartridge lists are updated automatically at the following intervals:

* The node's database is refreshed every minute.
* The broker's cache is refreshed every six hours.
* The console's cache is refreshed every five minutes.

In order to clear the cache for both the broker and management console at the same time, enter in the following command:

**Note:** Execute the following on the broker host.

	# oo-admin-broker-cache --clear --console
	
You should see the following confirmation message:

	Clearing broker cache.
	Clearing console cache.
	
It may take several minutes before you see the new cartridges available on the management console as it takes a few minutes for the cache to completely clear.

##**Testing new cartridges**

Given the steps in Lab 5 of this training, you should be able to access the management console from a web browser using your local machine.  Open up your preferred browser and enter the following URL:

	http://broker.hosts.example.com
	
You will be prompted to authenticate and then be presented with an application creation screen.  After the cache has been cleared, and assuming you have added the new cartridges correctly, you should see a screen similar to the following:

![](http://training-onpaas.rhcloud.com/ose2/addCartridgeWebConsole.png)

If you do not see the new cartridges available on the management console, check that the new cartridges are available by viewing the contents of the */usr/libexec/openshift/cartridges* directory:

	# cd /usr/libexec/openshift/cartridges
	# ls

If you do not see the cartridge in this directory, the RPM was not installed correctly.

Check that the cartridges have been installed into the cartridge repository:

	# cd /var/lib/openshift/.cartridge_repository
	# ls
	# oo-admin-cartridge --list

If you do not see the cartridge in the *.cartridge_repository* directory and the output of *oo-admin-cartridge --list*, you may need to restart the *ruby193-mcollective* service.

Verify that your OpenShift node is reporting the correct list of cartridges to your OpenShift broker:

	# oo-mco rpc -q openshift cartridge_repository action=list

You may also need to clear the broker's and console's caches again, as described earlier in this lab.
	
##**Installing the PostgreSQL and DIY cartridges**

Using the knowledge that you have gained during in this lab, perform the necessary commands to install both the PostgreSQL and DIY cartridges on your node host.  Verify the success of the installation by ensuring that the DIY application type is available on the management console:

![](http://training.runcloudrun.com/images/console-diy.png)


**Lab 6 Complete!**
<!--BREAK-->
