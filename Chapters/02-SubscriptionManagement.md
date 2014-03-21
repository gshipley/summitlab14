#**Lab 2: Using the OpenShift Enterprise Subscription**

**Servers used:**

* broker host
* node host

**Tools used:**

* SSH
* subscription-manager
* yum

##**Verifying Red Hat Subscription Manager Channels**

In order to be able to update to newer packages, and to download the OpenShift Enterprise software, your system will need to be registered with Red Hat to grant your system access to appropriate software channels.  The machines provided to you in this class have already been registered with the production Red Hat Subscription Manager, and the channels you will need have already been added.

Please verify now that your machines have the required channels enabled:

**Note:** Execute the following on both of the hosts that have been provided to you

	# yum repolist

You should see the following list of channels:

	rhel-6-server-cf-tools-1-rpms      Red Hat CloudForms Tools for RHEL 6 (RPMs)
	rhel-6-server-ose-2.0-infra-rpms   Red Hat OpenShift Enterprise 2.0 Infrastructure (RPMs)
	rhel-6-server-ose-2.0-node-rpms    Red Hat OpenShift Enterprise 2.0 Application Node (RPMs)
	rhel-6-server-rpms                 Red Hat Enterprise Linux 6 Server (RPMs)

Note that if you were registered using Red Hat Network, the channels would be named using a different format.

**Lab 2 Complete!**

<!--BREAK-->
