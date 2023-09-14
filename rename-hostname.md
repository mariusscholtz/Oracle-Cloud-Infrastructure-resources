## Change hostname of OCI Compute Virtual Machine

Overview: 
This section explains how to change the hostname of a Virtual Machine (VM) created on Oracle Cloud Infrastructure (OCI). When a VM is created on OCI, the hostname will be set the same as the 'instance name'.

How to change hostname of VM on OCI. See How to Permanently Change Hostname in Oracle Cloud Infrastructure (OCI) Compute Environment on Oracle Linux (Doc ID 2325469.1)

In this example the hostname is set to: prod1
We want to change the hostname to: prodapp1

For Oracle Linux 7 and later, do the following:
1: Display current hostname using OCI console:
<img alt="prod1" src="images/prod1.png" title="Create instance" width="10%" style="float:right">

-->	Display current hostname using terminal:
	$ hostnamectl
	<show image>


2: Update the /etc/hostname file with the new hostname: prodapp1
  $ sudo hostnamectl set-hostname prodapp1

3: Edit the oci configuration file for hostnames as given below to update the needed value to 2.
  $ sudo vi /etc/oci-hostname.conf
    PRESERVE_HOSTINFO=2
    save the changes.

4: Edit the FQDN from OCI console GUI
go to compute instances on the OCI Console
--> select the instance
--> scroll down
--> at the left bottom corner select "Attached VNIC" under the Resources heading
--> edit appropriate VNIC
--> change hostname to update FQDN
--> update VNIC
--> Save changes
--> More action -> Edit shape -> Change instance name to prodapp1

5: Reboot the instance from the OCI console
$ sudo init 6

<Add image>

How to change timezone of a VM: Oracle Doc 2775348.1
In this example, the timezone will be changed to Africa/Johannesburg
$ sudo su -
$ sudo timedatectl list-timezones
$ sudo timedatectl set-timezone <desired_timezone>
$ sudo timedatectl set-timezone Africa/Johannesburg
$ ls -l /etc/localtime
$ date

<!-- <Add image>
<img alt="Cloud Computing" src="images/cloud-computing.png" title="Cloud Computing" width="10%" style="float:right">
-->


  References:
  
  [(1) How to change hostname of VM on OCI (Doc ID 2325469.1)](https://support.oracle.com/knowledge/Oracle%20Cloud/2325469_1.html)
  [(2) How to change timezone of VM on OCI (Doc ID 2775348.1)](https://support.oracle.com/knowledge/Oracle%20Cloud/2775348_1.html)



# License
Copyright (c) 2023 Oracle and/or its affiliates.
Licensed under the Universal Permissive License (UPL), Version 1.0.
See [LICENSE](https://github.com/oracle-devrel/technology-engineering/blob/folder-structure/LICENSE) for more details.


--September 2023
