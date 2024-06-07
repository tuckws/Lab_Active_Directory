## Lab_Active_Directory

#### Purpose
The purpose of this lab is to develop knowledge and experience working with Active Directory from initial setup to created users in groups with domain access. The steps outlined here provide a working model to develop enterprise level environments for future testing with external network integration, vulnerability assessment, threat tools and of course security configuration baseline learned from SANS and NIST/CIS.
#### Lessons Learned
- Active Directory installation and configuration for Windows Server 2019.
- DNS setup and configuration with Domain Controller.
- DHCP setup and configuration with Domain Controller.
- Created PowerShell script to add 1,000 users to an OU.
- 
#### Tools Used
- Active Directory.
- VMWare.
- Draw.io.
- PowerShell.
#### Steps
##### Initial Concept
I am creating a AD environment for the Acme Corporation. The environment will have approximately 1000 users. See the following network diagram for the plan:

![AD Network Diagram](https://imgur.com/a/0EJ6tRI "AD Network Diagram")

##### VMWare Setup
1. VMWare setup is relatively straight forward. Using eval versions for Windows 10 Pro and Windows Server 2019, I created 2x VMs within VMWare. Note that Windows Server 2019 serves as the domain controller which requires two NICs - internal and external in order to facilitate DNS and DHCP serving for the domain. 
