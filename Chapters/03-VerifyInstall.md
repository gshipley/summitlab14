
#**Lab 3: Verifying the installation**

**Servers used:**

* broker host
* node host

**Tools used:**

* *service*
* *cat*
* *ping*
* *oo-diagnostics*
* *oo-mco* 
* *mongo*
* *shutdown*
	
##**Rebooting the hosts**

Congratulations! You have just installed OpenShift Enterprise 2.0.  However, before proceeding any further in the lab manual, we need to verify that the installation was successful.  There are a couple of utilities that we can use to help with this task.  The first thing we want to check is that everything was installed and configured correctly to ensure that all services will be present after a reboot of the hosts.  To ensure that all services are started in the correct order, reboot the broker host first by going to the console of your broker host and entering in the following command:

**Note:** Perform the following command on the broker host.

	shutdown -r now

Wait for the system to restart.  Once the broker host has finished rebooting, it is safe to reboot the node host.  Go to the console of your node host and enter in the following command:

**Note:** Perform the following command on the node host.

	shutdown -r now
	
Wait for your node host to come back online.

##**Verifying the installation**

###**Verifying DNS nameserver**
Now that our broker and node hosts have been restarted, we can verify that some of the core services have started upon system boot.  The first one want to test is the named service, provided by *BIND*.  In order to test that *BIND* was started successfully, enter in the following command:

**Note:** Perform the following command on the broker host.

	service named status
	
You should see information similar to the following:

	version: 9.8.2rc1-RedHat-9.8.2-0.17.rc1.el6_4.6
	CPUs found: 2
	worker threads: 2
	number of zones: 8
	debug level: 0
	xfers running: 0
	xfers deferred: 0
	soa queries in progress: 0
	query logging is OFF
	recursive clients: 0/0/1000
	tcp clients: 0/100
	server is up and running
	named (pid  1106) is running...

If *BIND* is up and running, we can feel assured that there are no syntax errors in our zone files.

The next aspect of DNS that we can verify is the actual database for our DNS records.  There should be two database files that contain the domain information for our hosts.  Let's verify that the DNS information for our hosts.example.com zone has been setup correctly with the following command:

**Note:** Perform the following command on the broker host.

	cat /var/named/dynamic/hosts.example.com.db

The output should look similar to the following.

	$ORIGIN .
	$TTL 1	; 1 seconds (for testing only)
	hosts.example.com		IN SOA	broker.hosts.example.com. hostmaster.hosts.example.com. (
					2011112904 ; serial
					60         ; refresh (1 minute)
					15         ; retry (15 seconds)
					1800       ; expire (30 minutes)
					10         ; minimum (10 seconds)
					)
				NS	broker.hosts.example.com.
				MX	10 mail.hosts.example.com.
	$ORIGIN hosts.example.com.
	ns1			IN A	172.16.1.2
	broker			IN A	172.16.1.2
	activemq			IN A	172.16.1.2
	datastore			IN A	172.16.1.2
	node			IN A	172.16.1.3

Verify that you have entries for both the broker and the node host listed in the output of the *cat* command.  Once you have verified this, take a look at the database for the apps.example.com zone to verify that is has an *NS* entry for *broker.hosts.example.com*:

**Note:** Perform the following command on the broker host.

	cat /var/named/dynamic/apps.example.com.db

###**Verifying DNS resolution**

One of the most common problems that students encounter is improper host name resolution.  If this problem occurs, the broker will not be able to contact the node hosts and vice versa.  Both the broker and node host should be using your OpenShift broker host at 172.16.1.2 for DNS resolution.  To verify this, view the */etc/resolv.conf* file on both the broker and node host to verify it is pointed to the correct *BIND* server.

	cat /etc/resolv.conf

If everything with DNS is setup correctly, you should be able to ping node.hosts.example.com from the broker and receive a response, as shown below.

	ping node.hosts.example.com
	
 	PING node.hosts.example.com (209.132.178.69) 56(84) bytes of data.
	64 bytes from node.osop-local (209.132.178.69): icmp_seq=1 ttl=64 time=0.502 ms

###**Verifying ActiveMQ and MCollective**	

ActiveMQ is a fully open-source message service that is available for use across many different programming languages and environments.  MCollective is a higher-level framework for remote job execution that communicates over ActiveMQ.  OpenShift Enterprise makes use of these technologies to handle communications between the broker host and the node host in our deployment.

The first thing we want to ensure is that the ActiveMQ daemon is running.  Perform the following command on the broker host:

**Note:** Execute this command on the broker host

	service activemq status

The output of the above command should look similar to the following:

	ActiveMQ Broker is running (1211).

Now that we know that ActiveMQ is up and running, let's verify that MCollective is able to communicate between the broker and the node hosts.  To verify this, use the *oo-mco ping* command.

**Note:** Execute the following on the broker host

	oo-mco ping

If MCollective is working correctly, you should see the following output (the time values will vary).

	node.hosts.example.com                  time=114.73 ms
	
	
	---- ping statistics ----
	1 replies max: 114.73 min: 114.73 avg: 114.73


###**Using the *oo-diagnostics* utility**	

OpenShift Enterprise ships with a diagnostics utility that can check the health and state of your installation.  The utility may take a few minutes to run as it inspects your hosts.  In order to run this tool, simply enter the following command:
	
**Note:** Execute the following on both the broker host and the node host.

	oo-diagnostics

When running the *oo-diagnostics* script at this time, it is expected to see some warnings as we have not yet setup districts for our installation.  You may also see an error regarding the verification of the security certificate.

Note that you can run the same `oo-diagnostics` command on both the broker host and the node host because `oo-diagnostics` will detect the type of host on which it is executed and run only the tests that are appropriate for that type of host.

###**Calling the REST API**

OpenShift client tools, including the *rhc* command-line tool as well as the web- and IDE-based interfaces, all communicate with the broker using a RESTful HTTP-based interface.  Use the *curl* command to verify that the broker's REST interface is functional:

	curl -k -u demo:changeme https://broker.hosts.example.com/broker/rest/applications

You should see a short JSON response.

**Lab 3 Complete!**
<!--BREAK-->
