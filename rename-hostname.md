## Change hostname of OCI Compute Instance Virtual Machine

### Overview
This section explains how to change the hostname of a Virtual Machine (VM) created on Oracle Cloud Infrastructure (OCI). When a Virtual Machine (VM) is created on OCI, the hostname will be the same name as the 'Compute Instance name'.

### How to change hostname of VM on OCI

In this example the current hostname is set to: **prod1**

We want to change the hostname to: **prodapp1**

If the OCI Compute Instance VM is running Oracle Linux 7 and later, do the following:

1: Display current hostname using OCI console:
 <img alt="prod1" src="images/prod1-info1.png" title="Create instance" width="90%" style="float:right">

 <!-- <img alt="prod1" src="images/prod1.png" title="Create instance" width="90%" style="float:right"> -->

Display current hostname using terminal:
 $ hostnamectl

<img alt="prod1" src="images/prod1-hostnamectl-2.png" title="hostnamecl" width="90%" style="float:right">


2: Update the /etc/hostname file with the new hostname: prodapp1

  $ sudo hostnamectl set-hostname prodapp1

3: Edit the oci configuration file for hostnames as given below to update the needed value to 2.
  $ sudo vi /etc/oci-hostname.conf
    PRESERVE_HOSTINFO=2
    save the changes.

<pre>
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
</pre>
<img alt="prod1" src="images/edit-shape.png" title="Edit shape" width="90%" style="float:right">

<img alt="prod1" src="images/edit-vnic.png" title="Edit VNIC" width="90%" style="float:right">

<img alt="prod1" src="images/edit-vnic-more.png" title="Edit VNIC more" width="90%" style="float:right">



5: Reboot the instance to apply the new hostname. Use OCI Console to reboot the Compute instance:
$ sudo init 6

<img alt="prodapp1" src="images/prodapp1-oci-console.png" title="OCI COnsole prodapp1 info" width="90%" style="float:right">

<img alt="prodapp1" src="images/prodapp1.png" title="prodapp1 infor from terminal" width="90%" style="float:right">

### References:
  [(1) How to change hostname of VM on OCI (Doc ID 2325469.1)](https://support.oracle.com/knowledge/Oracle%20Cloud/2325469_1.html)

# License
Copyright (c) 2023 Oracle and/or its affiliates.
Licensed under the Universal Permissive License (UPL), Version 1.0.
See [LICENSE](https://github.com/oracle-devrel/technology-engineering/blob/folder-structure/LICENSE) for more details.


--September 2023
