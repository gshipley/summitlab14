#**Lab 6: Configuring RHC**

**Server used:**

* client host

**Tools used:**

* *rhc*

##**Connecting to the client host**

In this and subsequent labs, we will be working on the *lab7-client* virtual machine that has been provided to you.  You will want to connect to this VM using the *Virtual Machine Manager* in order to have access to its *GNOME* desktop and a graphical web browser.

Open GNOME's *Applications* menu, select *System Tools*, and select *Virtual Machine Manager*.  Make sure that the *lab7-client* VM is running, and then double-click it to access it.  Log in with the *demo* user and the password *changeme*.

##**Using *rhc setup***

By default, the RHC command-line tool will default to using the publicly hosted OpenShift environment (http://www.openshift.com).  Since we are using our own enterprise environment, we need to tell *rhc* to use our broker.hosts.example.com server instead of openshift.com.  In order to accomplish this, the first thing we need to do is run the *rhc setup* command using the optional `--server` parameter.

	rhc setup --server broker.hosts.example.com

Once you enter in that command, you will be prompted for the username that you would like to authenticate with.  For this training class, use the *demo* user account.  The password configured for this account is *changeme*.

The first thing that you will be prompted with will look like the following:

	The server's certificate is self-signed, which means that a secure connection can't be established to
	'broker.hosts.example.com'.
	
	You may bypass this check, but any data you send to the server could be intercepted by others.
	Connect without checking the certificate? (yes|no):

Since we are using a self-signed certificate, go ahead and select *yes* here and press the enter key.

At this point, you will be prompted for the username.  Enter in *demo* and specify the *changeme* password.

After authenticating, OpenShift Enterprise will prompt if you want to create a authentication token for your system.  This will allow you to execute command on the PaaS as a developer without having to authenticate.  It is suggested that you generate a token to speed up the other labs in this training class.

The next step in the setup process is to create and upload our SSH key to the broker server.  This is required for pushing your source code, via Git, up to the OpenShift Enterprise PaaS.

Finally, you will be asked to create a namespace for the provided user account.  The namespace is a unique name which becomes part of your application's URL. It is also commonly referred to as the user's domain. The namespace can be at most 16 characters long and can only contain alphanumeric characters. For this lab, create the following namespace:

	ose

##**Under the covers**

The *rhc setup* tool is a convenient command-line utility to ensure that the user's operating system is configured properly to create and manage applications from the command line.  After this command has been executed, a *.openshift/* directory will have been created in the user's home directory with some basic configuration items specified in the *express.conf* file.  The contents of that file are as follows:

	# Your OpenShift login name
	default_rhlogin=demo

	# The OpenShift server to connect to
	libra_server=broker.hosts.example.com

This information will be read by the *rhc* command line tool for every future command that is issued.  If you want to run commands as a different user than the one listed above, you can either change the default login in this file or provide the `-l` switch to the *rhc* command.


**Lab 6 Complete!**

<!--BREAK-->
