# Cluster software on Oracle Linux 9 using Corosync and Pacemaker

 Review date: 18 December 2023

# Overview
High-availability is the process of making data and applications available most of the time. In order to make data and applications highly available, a cluster of servers, or Virtual Machines are grouped together to run a service. If one node in the cluster fails, then the other node will takeover responsibility of the failed node. This document explains the method for creating a cluster using Corosync and Pacemaker software on Oracle Cloud Infrastructure. This cluster will make an IP address highly available. The IP address will run on one node and in the event of failure will failover over to the standby node automatically. This blog shows an [overview of clustering](https://blogs.oracle.com/cloud-infrastructure/post/automatic-virtual-ip-failover-on-oracle-cloud-infrastructure-looks-hard-but-it-isnt) 

Thank you to Michael van Dijk for his help and input. 

# Architecture
Two nodes will be deployed on Oracle Cloud Infrastructure
Each node in the cluster will run
- [Oracle Linux 9](https://docs.oracle.com/en/operating-systems/oracle-linux/9/)
- [Corosync and Pacemaker software](https://www.redhat.com/sysadmin/rhel-pacemaker-cluster)
- The cluster will consist of two Compute instances running on [Oracle Cloud Infrastructure](https://www.oracle.com/cloud/)  
- Each compute instance will run in a different [Fault Domain](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm). This cluster has successfully been tested using different Availability Domains.
- The cluster will be created on a public subnet for easier access and will run on the OCI provided private network.
  
The picture below shows a graphical representation
![cluster image](images/demo-cl-draw.png)
<!--  <img alt="cluster" src="images/demo-cl-drawio.png" width="10%" style="float:right"></img> -->


# Install and configure the Coroysync and Pacemaker cluster software
This installation assumes that the Compute Instances on OCI running Oracle Linux 9 have already been installed. If not, then follow this [procedure](https://docs.oracle.com/en/learn/oci-basics-tutorial/index.html#introduction) to install the compute instances. 

On both nodes in the cluster, ensure the operating system is up to date:
> $ sudo dnf update -y
  
Reboot the node when the update is finished:
> $ sudo init 6  

Change the default root prompt to show to full path of the current directory:
> $ sudo vi /root/.bashrc and include prompt for full path:
> PS1=$LOGNAME@`uname -n`:'$PWD # ';export PS1

> $ sudo vi /etc/hosts and add both nodes in the cluster, and ping the nodes to see if they are alive:
![hosts image](images/hosts.png)
<!-- <img alt="hosts.png" src="images/hosts.png" width="10%" style="float:right"> -->

## Install oci cli on both nodes in the cluster
First, copy your private key pem to each node in a folder, example /root/

For Oracle Linux 9, Configure and install oci cli for **root user**. Use this [document](https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/cliinstall.htm)
 for detail:
> $ sudo dnf -y install oraclelinux-developer-release-el9\
> $ sudo dnf install python39-oci-cli
Download the PEM file form OCI console → User profile. If not available, create a new PEM file: https://docs.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#Required_Keys_and_OCIDs 

Setup oci cli as the root user
> $ sudo oci setup config
<p>
[DEFAULT]<br>
user=ocid1.user.oc1...<br>
fingerprint=fa:0e:zzz4<br>
key_file=/root/oci_api_key.pem<br>
tenancy=ocid1.tenancy.oc1...<br>
region=eu-frankfurt-1<br>
pass_phrase=w******<br>
</p>

Test oci cli, for example get the namespace for Object storage:
![oci cli](images/oci-os-ns-get.png)

> $ sudo oci os ns get
> <p>
{<br>
  "data": "fr9...."<br>
}<br>
</p>


## Create the virtual IP on node1 using the OCI Console

On node1: Create a secondary VNIC using OCI console. Create on the same network as node1 and node2. 
Click OCI console → Compute instance → Attached VNIC 
![virtualip image](images/oci-console-virtualip1.png)

Note down the IP Address. In my case it is 10.6.0.151
See https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/private-ip-address-move-vnic.htm 

![virtualip2 image](images/oci-console-virtualip2.png)


Create the Virtual IP failover script, /root/move-fip.sh
![move-fip.sh image](images/move-fip.png)

<p>
#!/bin/sh<br>
# Virtual IP from OCI<br>
server="`hostname -s`"<br>
node1vnic="ocid1.vnic.oc1.eu-frankfurt-1.a..."<br>
node2vnic="ocid1.vnic.oc1.eu-frankfurt-1.b..."<br>
vnicip="10.6.0.151"<br>
export LC_ALL=C.UTF-8<br>
export LANG=C.UTF-8<br>
<br>
touch /tmp/error.log<br>
if [ $server = "node1" ]; then<br>
  oci network vnic assign-private-ip --unassign-if-already-assigned --vnic-id $node1vnic --ip-address $vnicip >/tmp/error.log 2>&1<br>
else<br>
  oci network vnic assign-private-ip --unassign-if-already-assigned --vnic-id $node2vnic --ip-address $vnicip >/tmp/error.log 2>&1<br>
fi
</p>


**Test manual failover using the move-fip.sh script:**
> $ sudo /root/move-fip.sh
> 
> $ sudo more /tmp/error.log
> 
> Taking no action as IP address 10.6.0.151 is already assigned to VNIC ocid1.vnic.oc1.eu-frankfurt-1.a...

![move-fip image](images/move-fip2.png)

## Install corosync and pacemaker software on both nodes:
See https://blogs.oracle.com/ateam/post/isv-implementation-details-part-4a-linux-clustering-with-pacemaker-and-corosync  
  
For Oracle Linux 8 or later: 
>	$ sudo dnf install  -y corosync pacemaker pcs fence-agents-all pcp-zeroconf resource-agents

On both nodes in the cluster, open firewall ports for Pacemaker cluster software:
> firewall-cmd --permanent --add-service=high-availability
> 
> firewall-cmd --add-service=high-availability
> 
> firewall-cmd --reload
> 
> firewall-cmd --list-all


Configure cluster password: (on both nodes in the cluster)
> $ sudo passwd hacluster 

enable and restart pcsd services on both nodes: 
> systemctl enable pcsd.service
> 
> systemctl start pcsd.service
>
> systemctl enable corosync.service
> 
> systemctl start corosync.service → This produced an error.
> 
> systemctl enable pacemaker.service
> 
> systemctl start pacemaker.service → This produced an error.
> 
> systemctl enable pcsd
> 
> systemctl start pcsd



Authorize the cluster nodes: On node1: 
> $ sudo pcs host auth node1 node2 (as per /etc/hosts).

It will ask for authorization, use hacluster and the password you set before. 
![pcs host auth image](images/pcs-host-auth.png)


If you get error unable to authenticate with node2 then make sure to add port 2224 as destination port on your security list for the network you are using:

![ingres-rules image](images/ingres-rules.png)


Create the cluster on node1
> $ sudo pcs cluster setup --start oci-cl node1 node2

![pcs-cluster-setup image](images/pcs-cluster-setup.png)

On node1: Start the cluster: 
> $ sudo pcs cluster start node1 node2

Check cluster status: pcs cluster status or type: sudo pcs status 

Disabling STONITH and Ignoring Quorum:
> $ sudo pcs property set stonith-enabled=false 
> $ sudo pcs property set no-quorum-policy=ignore

On node1: Setup the cluster to start automatically on reboot: 
> $ sudo pcs cluster enable --all

![pcs-cluster-enable image](images/pcs-cluster-enable.png)


Display cluster status on node1, run: 
> $ sudo pcs status

![pcs-cluster-status image](images/pcs-cluster-status-no-res.png)


Create dummy service and test failover:
https://docs.oracle.com/en/operating-systems/oracle-linux/9/availability/OL9-AVAILABILITY.pdf 
On node1: 
> $ sudo pcs resource create dummy-service ocf:pacemaker:Dummy op monitor interval=120s

![pcs-dummy-service image](images/pcs-dummy-service.png)




Move the dummy-service resource to node2 to test failover:
> $ sudo pcs resource move dummy-service node2

![pcs-dummy-service-move image](images/pcs-dummy-service-move.png)


**Create the Virtual IP failover resource**
On both nodes, Add Virtual IP failover script to /usr/lib/ocf/resource.d/heartbeat/IPaddr2

> $ sudo cp /usr/lib/ocf/resource.d/heartbeat/IPaddr2 /usr/lib/ocf/resource.d/heartbeat/IPaddr2.orig<br>
> $ sudo vi /usr/lib/ocf/resource.d/heartbeat/IPaddr2<br>
<p>
Look for the section that says “Add an interface” and call the /root/move-fip.sh script: Approximately on line 683<br>
<br>
#        Add an interface<br>
#<br>
add_interface () {<br>
        local cmd msg ipaddr netmask broadcast iface label<br>
<br>
        ipaddr="$1"<br>
        netmask="$2"<br>
        broadcast="$3"<br>
        iface="$4"<br>
        label="$5"<br>
<br>
# Virtual IP failover script here - start<br>
        /root/move-fip.sh<br>
# Virtual IP failover script entry - end<br>
<br>
        if [ "$FAMILY" = "inet" ] && ocf_is_true $OCF_RESKEY_run_arping &&<br>
           check_binary arping; then<br>
	….. # The rest of the IPAddr2 script is not shown.<br>
</p>


On node1, Test the /root/move-fip.sh script 
This will move the virtual ip 10.6.0.241 from node2 to node1. The best way to view this is to open the OCI console, Compute → Node1 → Attached VNIC → IP Addresses

When the script works, it means that oci cli works and the IP fails over to the other node in the cluster.

Note: The virtual IP will not be ping-able at this stage, only when the pcs cluster resource is created, then it you can ping the virtual IP host. You can, however, see the virtual IP address if you run ‘ip addr’ from node1. See below.

Once the move-fip.sh script works on both nodes, then you can create the cluster virtual IP:
Create Virtual IP resource:
> $ sudo pcs resource create virtualip IPaddr2 ip=10.6.0.151  cidr_netmask=24 op monitor interval=30s

![pcs-virtualip-create image](images/pcs-create-resource-virtualip.png)

ping the virtual IP and see if it is alive

![ping-virtualip image](images/ping-virtualip.png)

Move the virtualip that is running on node1 to node2

![move-virtualip-res image](images/pcs-move-virtualip-to-node2.png)

Display the virtualip resource detail: 
> $ sudo pcs resource config virtualip

![virtualip-pcs-resource-confg image](images/pcs-resource-config.png)

You can now use the failover IP 10.6.0.151 for your applications. For example run your Apache web on this virtualip.

## Web interface port 2224
You can use the web interface to create, configure, and manage Pacemaker clusters.

To access the web UI, log in as user ‘hacluster’ at https://node:2224. Specify the node either by its node name or IP address.

Docs: 
https://docs.oracle.com/en/operating-systems/oracle-linux/9/availability/availability-UsingthePacemakerCorosyncWebUserInterface.html#using-webui 
Open more ports: 2224. See https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-firewalls-haar 

On OCI, I opened port 2224,3121,5403,21064,9929 TCP. I also opened ports 5404,5405,9929 on UDP, but could not get access to the Web UI. I had to open all ports 0.0.0.0/0 on my OCI public subnet in order to access the Web UI via the public IP.

Check if the port is open:
> $ sudo netstat -tnualp|grep 2224

Here are some screenshots to the Web UI: 
![webui1 image](images/webui1.png)
![webui2 image](images/webui2.png)



# Useful Links
- [Cluster example on Private Cloud Appliance](https://docs.oracle.com/en/solutions/private-cloud-appliance-virtual-ip-failover/index.html#GUID-5398321F-5B82-4869-8630-914907E01E0D)
- [Overview of clustering](https://blogs.oracle.com/cloud-infrastructure/post/automatic-virtual-ip-failover-on-oracle-cloud-infrastructure-looks-hard-but-it-isnt)

# License
Copyright (c) 2023 Oracle and/or its affiliates.
Licensed under the Universal Permissive License (UPL), Version 1.0.
See [LICENSE](https://github.com/oracle-devrel/technology-engineering/blob/folder-structure/LICENSE) for more details.

--oooOooo---

