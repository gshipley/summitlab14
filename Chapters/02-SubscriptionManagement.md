#**Lab 2: Using the OpenShift Enterprise Subscription**

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

In order to be able to update to newer packages, and to download the OpenShift Enterprise software, your system will need to be registered with Red Hat to allow your system access to appropriate software channels.  You will need the following subscription at a minimum for this class.

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
	
Perform the subscription process on the node host as well.
	
**Lab 2 Complete!**

<!--BREAK-->
