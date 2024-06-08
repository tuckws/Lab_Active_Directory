## Lab_Active_Directory

#### Purpose
The purpose of this lab is to develop knowledge and experience working with Active Directory from initial setup to created users in groups with domain access. The steps outlined here provide a working model to develop enterprise level environments for future testing with external network integration, vulnerability assessment, threat tools and of course security configuration baselines learned from SANS and NIST/CIS.
#### Lessons Learned
- Active Directory installation and configuration for Windows Server 2019.
- DNS setup and configuration with Domain Controller.
- DHCP setup and configuration with Domain Controller.
- User/Admin/OU/Group Policy Object management for both Local and Domain.
- Created PowerShell script to add 1,000 users to an OU.
#### Tools Used
- Active Directory.
- VMWare.
- Draw.io.
- PowerShell.
#### Steps
##### Initial Concept
We are creating an AD environment for the Acme Corporation (Yes, I watched Looney Tunes as a kid). The environment will have approximately 1000 users. See the following network diagram for the plan:

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
4. Then, we select Configure under the DC-01 which is the name of the computer and user for this DC account. Note that IPv4 and IPv6 will not appear yet. Select NAT when problem and then select our INTERNET interface which will facilitate WAN to LAN NAT functionality for this domain.
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_RAS-NAS_Setup_2.png?raw=true)
##### DHCP Configuration
1. With NAT setup, DHCP will need to be configured. We now need to use the Add Roles and Features to enable DHCP, like we did for AD and Remote Access. 
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_DHCP_Setup.png?raw=true)
3. After that, going back to the Tools menu in Server Manager, we select DHCP to open up the interface for configuration.
4. Under IPv4 Server Options, we configure DNS to `172.16.0.1`.
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_DHCP_Setup_DNS_Config.png?raw=true)
5. In the next menu, we configure the router/gateway for our DC `172.16.0.1`.
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_DHCP_Setup_Gateway.png?raw=true)
6. Then, we add the Scope (IP Range).
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_DHCP_Setup_Scope.png?raw=true)
7. Having issues, I went back to Server Options to specifically add the Router IP `172.16.0.1`. 
##### Adding Domain Admin
1. We go to Active Directory Users and Computers in order to manage users. We add an ADMIN OU to our domain and create a admin user. We need to go to the user profile in order to add the ADMIN Group Policy Object (GPO) to enable privileges. Bad practice to use the actual Default Domain Admin for security and sysadmin purposes. 
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_Admi_%20Acct_Setup.png?raw=true)
##### Configuring Internet Browsing
1. Because this is not a production environment, we will disable IE-ESC restrictions which will enable full browsing on the Internet. Not secure.
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_Enable_Internet_Browsing.png?raw=true)
##### Automating User Creation with PowerShell
1. Using a PowerShell script with a generated random namelist, let's fill out our domain with users. We can create groups and policies later for fun. 
```
$PASSWORD_FOR_USERS = "Password1"
$USER_FIRST_LAST_LIST = Get-Content .\names.txt

$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force
New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false

foreach ($n in $USER_FIRST_LAST_LIST) {
    $first = $n.Split(" ")[0].ToLower()
    $last = $n.Split(" ")[1].ToLower()
    $username = "$($first.Substring(0,1))$($last)".ToLower()
    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan

    New-ADUser -AccountPassword $password `
               -GivenName $first `
               -Surname $last `
               -DisplayName $username `
               -Name $username `
               -EmployeeId $username `
               -PasswordNeverExpires $true `
               -Path "ou=_USERS,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
}
```
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD%20User%20Creation%20PS%20Script.png?raw=true)
##### Adding Another Computer to Domain
1. With the Domain setup, DC configured, ADMIN established, we launch our Windows 10 host. We configure the computer name under Advanced System Properties to change the hostname and add the host to our new domain. We could have added the Computer in a similar way we created Users. Need the MAC for that. 
![](https://github.com/tuckws/Lab_Active_Directory/blob/main/images/AD_Add-Change_Computer_to_Domain.png?raw=true)
##### Troubleshooting
1. Ensure we go on DC-01 and CLIENT-001 in order to `ping` both hosts and to `ping` the Internet. Both communicate internally and externally.
##### Now What?
1. At this point, we can apply NIST and CIS framework to our enterprise, setup a SIEM, attack the network, test, break, rebuild, play, etc. More to come. 
