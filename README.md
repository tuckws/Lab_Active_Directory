## Lab_Active_Directory

#### Purpose
The purpose of this lab is to develop knowledge and experience working with Active Directory from initial setup to created users in groups with domain access. The steps outlined here provide a working model to develop enterprise level environments for future testing with external network integration, vulnerability assessment, threat tools and of course security configuration baseline learned from SANS and NIST/CIS.
#### Lessons Learned
- Active Directory installation and configuration for Windows Server 2019.
- DNS setup and configuration with Domain Controller.
- DHCP setup and configuration with Domain Controller.
- Created PowerShell script to add 1,000 users to an OU.
#### Tools Used
- Active Directory.
- VMWare.
- Draw.io.
- PowerShell.
#### Steps
##### Initial Concept
We are creating a AD environment for the Acme Corporation. The environment will have approximately 1000 users. See the following network diagram for the plan:
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_Network_Diagram_v1.jpg?raw=true)
##### VMWare Setup
1. VMWare setup is relatively straight forward. Using eval versions for Windows 10 Pro and Windows Server 2019, we created 2x VMs within VMWare. 
2. Note that Windows Server 2019 serves as the domain controller (DC) which requires two NICs - internal and external in order to facilitate DNS and DHCP serving for the domain. The DC will have a NAT to network to our external WAN (Internet) and a Host-Only for the internal network.
##### Initial Network Configuration and Active Directory Installation
1. Having logged in, go to Network Connections in order to configure the network adapters. We will change the external network name to INTERNET and the internal network to INTERNAL.
2. For the INTERNAL network, we need to set the IPv4 to a static `172.16.0.1`, Subnet Mask `255.255.255.0`, Default Gateway blank since we are using the external NIC for this, and DNS `127.0.0.1` since the DC will be serving DHCP/DNS. 
3. Now, we need to install domain resources onto this server. It is not enabled by default. Within the Server Manager tool, we can go to Add Roles and Features to initialize this server as a DC. Note that there are many features within AD that can be enabled or disabled within this panel.
4. We are adding the forest with root domain: acmecorp.com.
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_Setup%201.png?raw=true)
##### RAS/NAT Configuration
1. Let's configure routing and remote access in order to enable NAT for our hosts on this domain. 
2. Going back to the Add Roles and Features, let's enable Remote Access.
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_RAS-NAS_Setup.png?raw=true)
3. Under Tools in the Server Manager, we can select Routing and Remote Access. 
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_RAS-NAS_Select_Routing_and_Remote_Access.png?raw=true)
4. Then, we select Configure for the IPv4 under the DC-01 which is the name of the computer and user for this DC account.
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_RAS-NAS_Setup_2.png?raw=true)
4. 
