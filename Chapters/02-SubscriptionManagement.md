#**Lab 2: Using the OpenShift Enterprise Subscription (Estimated time: XXX minutes)**

**Servers used:**

* broker host
* node host

**Tools used:**

* SSH
* subscription-manager
* yum
* ssh-keygen
* ssh-copy-id
* sh


##**Registering the system and adding subscriptions**

In order to be able to update to newer packages, and to download the OpenShift Enterprise software, your system will need to be registered with Red Hat to allow your system access to appropriate software channels.  You will need the following subscriptions at a minimum for this class.

* OpenShift Enterprise Employee Subscription

The machines provided to you in this lab have already been registered with the production Red Hat Network.  However, they have not been enabled for the above subscriptions.  List all of the available subscriptions for the account that has been registered for you:

**Note:** Execute the following on the broker host

	# subscription-manager list --available	
	
From the list provided, subscribe to the OpenShift Enterprise Employee subscription.  

	# subscription-manager attach --pool {pool id from the above command}

Once you have attached to correct pool, verify that you are subscribed to OpenShift Enterprise.

	# subscription-manager list --consumed
	
Also, take note of the yum repositories that you are now able to install packages from.

	# yum repolist

The output on the broker host should look similar to the following:

	rhel-x86_64-server-6
	rhel-x86_64-server-6-ose-2.0-infrastructure
	rhel-x86_64-server-6-ose-2.0-rhc
	rhel-x86_64-server-6-rhscl-1

Perform the subscription process on the node host as well.

The output on the node host should look similar to the following:

	jb-ews-2-x86_64-server-6-rpm
	jbappplatform-6-x86_64-server-6-rpm
	rhel-x86_64-server-6
	rhel-x86_64-server-6-ose-2.0-jbosseap
	rhel-x86_64-server-6-ose-2.0-node
	rhel-x86_64-server-6-ose-2.0-rhc
	rhel-x86_64-server-6-rhscl-1
	
##**Updating the operating system to the latest packages**

We need to update the operating system to have all of the latest packages that may be in the yum repository for RHEL Server. This is important to ensure that you have a recent update to the SELinux packages that OpenShift Enterprise relies on. In order to update your system, issue the following command:

	# yum update
	
**Note:** Depending on your connection and speed of your broker host, this installation make take several minutes.

##**Creating SSH keys**

In order for the *oo-install* tool to work correctly, we need to have SSH keys available on the both the broker and the node host to allow for authentication without a password.  In order to accomplish this task, we will need to generate a ssh keypair and then copy those keys to both the broker and node hosts.  Perform the following steps:

**Note:** Execute the following on the broker host

	# ssh-keygen -t rsa
	
Use the defaults and do not use a password for the key

	# ssh-copy-id -i ~/.ssh/id_rsa root@{yourBrokerIPAddress}
	
After entering in the above command, you will be prompted for the root password.  This password has been provided to you by the instructor of this course.  Enter the password and ensure that no errors occurred.  If everything worked correctly, let's copy the key to the node host as well.

	# ssh-copy-id -i ~/.ssh/id_rsa root@{yourNodeIPAddress}
	
In order to verify that the SSH keys were copied correctly, SSH into the node host from the broker and ensure that no password was required:

**Note:** Execute the following on the broker host

	# ssh root@{yourNodeIPAddress}
	
You should have been authenticated to the node host without requiring a password.  If this is not the case, please alert your instructor so you can troubleshoot the issue.

Disconnect from the node host by exiting your SSH session to the node host.

	# exit

	
**Lab 2 Complete!**

<!--BREAK-->

