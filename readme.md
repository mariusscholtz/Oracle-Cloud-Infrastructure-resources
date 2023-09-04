# Oracle Cloud Infrastructure Resources

Disclaimer
ORACLE AND ITS AFFILIATES DO NOT PROVIDE ANY WARRANTY WHATSOEVER, EXPRESS OR IMPLIED, FOR ANY SOFTWARE, MATERIAL OR CONTENT OF ANY KIND CONTAINED OR PRODUCED WITHIN THIS REPOSITORY, AND IN PARTICULAR SPECIFICALLY DISCLAIM ANY AND ALL IMPLIED WARRANTIES OF TITLE, NON-INFRINGEMENT, MERCHANTABILITY, AND FITNESS FOR A PARTICULAR PURPOSE. FURTHERMORE, ORACLE AND ITS AFFILIATES DO NOT REPRESENT THAT ANY CUSTOMARY SECURITY REVIEW HAS BEEN PERFORMED WITH RESPECT TO ANY SOFTWARE, MATERIAL OR CONTENT CONTAINED OR PRODUCED WITHIN THIS REPOSITORY. IN ADDITION, AND WITHOUT LIMITING THE FOREGOING, THIRD PARTIES MAY HAVE POSTED SOFTWARE, MATERIAL OR CONTENT TO THIS REPOSITORY WITHOUT ANY REVIEW. USE AT YOUR OWN RISK.


# Oracle Cloud Infrastructure (OCI) resources

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

--September 2023
