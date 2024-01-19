# Oracle Cloud Infrastructure (OCI) resources
This page displays information and technical resources relating to Oracle Cloud Infrastructure.

<i> Review Date: 19 January 2024</i>

# Table of content
1. [OCI Compute shape naming convention](https://github.com/mariusscholtz/Oracle-Cloud-Infrastructure-resources#oci-compute-shape-naming-convention)
2. [Rename the Hostname for OCI Compute shape running Oracle Linux](https://github.com/mariusscholtz/Oracle-Cloud-Infrastructure-resources#rename-the-hostname-for-oci-compute-shape-running-oracle-linux)
3. [What is an OCPU - Oracle Compute processing unit](https://github.com/mariusscholtz/Oracle-Cloud-Infrastructure-resources/blob/main/what-is-ocpu-vcpu.md) 
4. [Building a Linux cluster using Corosync and Pacemaker](https://github.com/mariusscholtz/Oracle-Cloud-Infrastructure-resources/blob/main/cluster/readme.md)
4. [Migrate a Compute shape from VM.Standard2.x to flexible shape on OCI](https://github.com/mariusscholtz/Oracle-Cloud-Infrastructure-resources/blob/main/VM-shapes/Migrate-VM.Standard2%20to%20Flex%20shape.pdf)


## OCI Compute shape naming convention
Overview: 
Oracle OCI offers several CPU architecture for customers to deploy their workloads. This writeup explains the naming convention of the OCI Compute shapes.

Compute shape naming convention:
A  Compute shape is a template that determines the number of cores or Oracle Compute Processing Unites (OCPUs), amount of memory, and other resources that are allocated to an instance. Compute shapes are available with AMD processors, Intel processors, and Arm-based processors. <sup>(1)</sup>

In OCI, Oracle offers several Compute shapes with the following naming convention:

    • VM.Standard.E2, VM.Standard.E3, VM.Standard.E4, or VM.Standard.E5 refers to AMD Epyc processing architecture
    • VM.Standard3, or VM.Standard4 refers to Intel processing architecture
    • VM.Standard.A1 refers to ARM Ampere processing architecture


  References:
  
  <sup>[(1)</sup> OCI Compute Shapes detail:](https://docs.oracle.com/en-us/iaas/Content/Compute/References/computeshapes.htm) https://docs.oracle.com/en-us/iaas/Content/Compute/References/computeshapes.htm
  
  <sup>[(2)</sup> Oracle PaaS and IaaS Universal Credits Service Descriptions:](https://www.oracle.com/assets/paas-iaas-universal-credits-3940775.pdf) https://www.oracle.com/assets/paas-iaas-universal-credits-3940775.pdf


## Rename the Hostname for OCI Compute shape running Oracle Linux
Overview: 
Renaming a virtual machine hostname is very simple on Oracle Cloud Infrastructure (OCI).
This [process](https://github.com/mariusscholtz/Oracle-Cloud-Infrastructure-resources/blob/main/rename-hostname.md) explains how to rename the hostname on Oracle Linux, and also renaming the Compute instance shape name.


## What is an OCPU? Oracle Compute processing unit
Overview: 
An OCPU is the unit used by Oracle when performing sizing and [costing](https://www.oracle.com/cloud/costestimator.html) for cloud environments. An OCPU is equivalent to one core. 
This [page](https://github.com/mariusscholtz/Oracle-Cloud-Infrastructure-resources/blob/main/what-is-ocpu-vcpu.md) explains in more detial the correlation between Core, VCPU, and OCPU.

# Licensing
Copyright (c) 2023 Oracle and/or its affiliates.

Licensed under the Universal Permissive License (UPL), Version 1.0.

See [LICENSE](https://github.com/oracle-devrel/technology-engineering/blob/main/LICENSE) for more details.

