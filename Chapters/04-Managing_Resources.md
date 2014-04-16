#**Lab 4: Managing resources**

**Server used:**

* broker host

**Tools used:**

* *cat*
* text editor
* *oo-admin-ctl-user*

##**Setting default gear quotas and sizes**

A user's default gear size and quota are specified in the */etc/openshift/broker.conf* configuration file located on the broker host.

The *VALID_GEAR_SIZES* setting is not applied to users but rather specifies the gear sizes that the current OpenShift Enterprise PaaS installation supports.

The *DEFAULT_MAX_GEARS* setting specifies the number of gears to assign to a new user upon that user's creation.  This is the total number of gears that a user can create by default.

The *DEFAULT_GEAR_SIZE* setting is the default size for gears when the user creates a new application.

The *DEFAULT_MAX_DOMAINS* setting specifies the max number of domains that one user can create.

Take a look at the  */etc/openshift/broker.conf* configuration file to determine the current settings for your installation:

**Note:** Execute the following on the broker host.

	cat /etc/openshift/broker.conf

By default, OpenShift Enterprise sets the default gear size to small, the number of gears a user can create to 100, and the maximum number of domains to 10.

When changing the */etc/openshift/broker.conf* configuration file, keep in mind that the existing settings are cached until you restart the *openshift-broker* service.

##**Setting the number of gears a specific user can create**

There are often times when you want to increase or decrease the number of gears a particular user can consume without modifying the setting for all existing users.  OpenShift Enterprise provides a command that allows the administrator to configure settings for an individual user.  To see all of the available actions that can be performed on a specific user, enter the following command:

	oo-admin-ctl-user

To see how many gears our *demo* user has consumed as well as how many gears the *demo* user has access to create, you can provide the following switch to the *oo-admin-ctl-user* command:

	oo-admin-ctl-user -l demo

Given the current state of our configuration for this training class, you should see the following output:

	User demo:
	    ...
	    consumed gears: 0
	    max gears: 100
	    gear sizes: small
	    ...

**Note:** The above output has been abbreviated.

In order to change the number of gears that our *demo* user has permission to create, you can pass the `--setmaxgears` switch to the command.  For instance, if we only want to allow the *demo* user to be able to create 25 gears, we would use the following command:

	oo-admin-ctl-user -l demo --setmaxgears 25

After entering the above command, you should see the following output (abbreviated):

	Setting max_gears to 25... Done.
	User demo:
	    ...
	    consumed gears: 0
	    max gears: 25
	    gear sizes: small
	    ...


##**Defining new gear types**

Each OpenShift node can only have one gear size associated with it.  When referring to the gear size associated with a particular node, we use the term "node profile." In order to add new gear sizes to your OpenShift Enterprise 2.0 installation, you will need to do two things:

* Create and define the new node profile on the node host.
* Update the list of valid node profiles (gear sizes) on the broker host.

To define a new gear size (node profile) in a multi-node setup, you would edit the */etc/openshift/broker.conf* file on each broker host to specify the allowed gear sizes and then modify the node profile in the */etc/openshift/resource_limits.conf* file on each node that you would like to host that gear size to match the name and sizing you would like.

##**Setting the type of gears a specific user can create**

**Note:** The below information is for informational purposes only.  During this lab, we are only working with one node host and therefore cannot add additional gear sizes.

In a production environment, a customer will typically have different gear sizes that are available for developers to consume.  For this lab, we will only create small gears.  However, to add the ability to create medium size gears for the *demo* user, you can pass the `--addgearsize` switch to the *oo-admin-ctl-user* command:

	oo-admin-ctl-user -l demo --addgearsize medium

After entering the above command, you would see the following output:

	Adding gear size medium for user demo... Done.
	User demo:
	  consumed gears: 0
	  max gears: 25
	  gear sizes: small, medium

In order to remove the ability for a user to create a specific gear size, you can use the `--removegearsize` switch:

	oo-admin-ctl-user -l demo --removegearsize medium



**Lab 4 Complete!**
<!--BREAK-->

