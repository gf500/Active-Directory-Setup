# Active-Directory
Active Directory Home-Lab

This project demonstrates the setup and configuration of an Active Directory home-lab environment for ACME, a fictional company. The company's headquarters is located in Montreal with a branch office in Boston. By setting up this lab environment, we aim to showcase the implementation of an AD infrastructure that reflects the organizational structure of a large-scale company. This readme provides an overview of the project and guides you through the steps to recreate the lab setup and configuration.

## Lab Environment Setup
For our starting environment, we're using VMware Workstation to create a virtualized setup for our Active Directory home-lab. We've set up two virtual machines to simulate our network, we will be adding another VM for the Boston Domain Controller later on.


Windows Server 2022 VM:
- This VM acts as the domain controller for the Montreal headquarters, managing user accounts, group policies, and other Active Directory services.

Windows 10 Workstation VM:
- This VM represents a regular workstation within the ACME network. It runs Windows 10 and allows us to test and demonstrate the features of our Active Directory infrastructure.

We assigned these static addresses for our lab
Network: 192.168.10.0
Default Gateway: 192.168.10.1
SRV-MTLDC01: 192.168.10.10
Workstation: 192.168.10.2

## Domain Controller

We will first change our server hostname and then promote our Windows Server VM to a domain controller and create our acme.com forest:

1. Change Hostname:
      - In the Windows Server VM settings, click on "Rename this PC."
      - Rename our machine to SRV-MTLDC01, where SRV stands for Server, MTL represents Montreal, DC represents Domain Controller, and 01 is the server number.
      - Restart the VM for the changes to take effect.

2. Promoting the server to Domain Controller:
    
   - In Server Manager, click on "Add roles and features", in the Server Roles tab select Active Directory Domain Services.
   - We keep features as is and Next, we will click “Next” and “Install” to finish up the ADDS install. 
   - In Server Manager, in the top right corner we now have a new yellow triangle, clicking on this, we see a pending Post-deployment Configuration option called “Promote this server to a Domain Controller”.
   - Select "Promote this server to a domain controller".
   - Choose the option to add a new forest since we are starting from scratch and enter the root domain name as acme.com.

![image](https://github.com/gf500/Active-Directory-Setup/assets/121585575/015735d6-0afa-4020-8acb-db2543e39579)

   - Click Next, we can leave the options as-is, add a DSRM password and click Next again.

![image](https://github.com/gf500/Active-Directory/assets/121585575/9180c4a6-bb25-4008-94a2-e9b82b090f5c)

   - We will leave all remaining options as-is and continue clicking Next until Install.
   - Once the installation completed you will be brought back to the login screen.

We now have a SRV-MTLDC01 serving as the domain controller for the acme.com root domain.

## Adding the Workstation VM to the Domain.

Our Windows 10 VM has a clean instal and a local_admin account, we now need to add it to the domain, allowing users to log in with their domain credentials and access domain resources.

1. Change Computer Name:

   - In the search bar, type "About your PC" and press enter.
   - Click on "Rename this PC".
   - In the Rename your PC window, enter "WRK-MTL01" as the new computer name.
   - Click "OK" to save the changes. You will be prompted to restart the workstation for the changes to take effect.
  
2. Verify Connectivity:
   - Open a Command Prompt on the workstation.
   - Type the command "ping 192.168.10.10" and press Enter.
   - Verify that the workstation can successfully ping the domain controller (SRV-MTLDC01) to ensure network connectivity.

Note: For the workstation to be added to the domain it must resolve the name of the domain, acme.com, to do so make sure you have the workstation's "Preferred DNS server" pointing to the Domain Controller IP address.

3. Add Workstation to the Domain:
   - In the "System Properties" window, go to the "Computer Name" tab and click on the "Change" button next to "To rename this computer or change its domain...".
   - In the "Computer Name/Domain Changes" window, select the "Domain" option.
   - Enter the name of your domain, acme.com in the "Domain" field.
   - Click "OK" to proceed.
   - Provide the credentials of a user account with permission to join workstations to the domain when prompted.
   - Click "OK" to join the workstation to the domain.
   - Restart the workstation for the changes to take effect.


## DNS Setup

### DNS Forwarder

As our domain controller serves as the DNS server for our users within our domain, we need to address the case where our users want to access websites and resources outside our network. In such cases, the domain controller needs to be configured to handle DNS queries for external domain names by utilizing DNS forwarders.

1. Add new conditional forwarder
   - In Server Manager, click Tools and select DNS, you should see the DNS Manager window.

![image](https://github.com/gf500/Active-Directory/assets/121585575/4961e4d5-84b6-44ea-9637-2be06d0e9eb5)
     
   - Right-click on the Conditional Forwarder folder and select New Conditional Forwarder.
   - In the "DNS Domain" field, enter the DNS domain you want to forward requests to. In our case we will enter google's DNS address at 8.8.8.8.
     

![image](https://github.com/gf500/Active-Directory/assets/121585575/5218bcf6-782a-4283-a75c-f6fd345d6058)
   
   - Click "OK" to save the forwarder settings.
   - We can add other DNS options for redundancy, including Cloudflare's 1.1.1.1 or quad9 at 9.9.9.9.
  
## DHCP Setup

We will now set up the DHCP on the domain controller to automate the assignment of IP addresses and network configuration settings to client devices. It will eliminate some of the tedious task of manually configuring network settings for each devices.

1. Installation
   - In Server Manager, click on "Add Roles and Features".
   - Navigate to the "Server Roles" section and select "DHCP Server" from the list of available roles.
   - Click "Next" to proceed until installation.
   - Once the installation is complete, click "Close" to exit the wizard.
  
2. Authorization
   - Once installed, we will have a Post-deployment configuration yellow notification in our Server Manager Dashboard.
   - Select it and click "Complete DHCP configuration"

![image](https://github.com/gf500/Active-Directory/assets/121585575/e7c292f0-a2c5-4e16-b850-c607a9c6a6fc)

   - This post-installation wizard will let us authorize the server to provide DHCP services, click "Next" and "Commit".

3. Scope Configuration
   - Back in Server Manager, under Tools select DHCP.

![image](https://github.com/gf500/Active-Directory/assets/121585575/8ccc0e73-f7cf-4696-86ad-75e166e984f4)

   - In the DHCP management console, locate the "IPv4" section under the server name.
   - Right-click on "IPv4" and select "New Scope" to create a new DHCP scope.
   - Follow the prompts in the New Scope Wizard to configure the DHCP scope settings, in our case we will name it Montreal-HQ with its network address as the description.

![image](https://github.com/gf500/Active-Directory/assets/121585575/852fa8c0-4fcd-4cfc-b2a4-5899856866ea)

   - For our address range we are going to start at .20 so we may have some room for other specific servers.

![image](https://github.com/gf500/Active-Directory/assets/121585575/d079901c-6343-49fe-8a21-ddd4629cfd0a)

   - We are going to leave the Exlusions and Delay as-is since we already excluded a batch of addresses from our range.

![image](https://github.com/gf500/Active-Directory/assets/121585575/7f5c6c79-e4e2-47b1-aaf3-ffa928855951)

   - We click "Next" until the Router (Default Gateway) window, here we enter the gateway's address, in our case 192.168.10.1.

![image](https://github.com/gf500/Active-Directory/assets/121585575/74f761f7-fc31-41cd-a961-8d7df0bd1bcf)

   - We can leave the Domain Name and DNS Servers section as-is since it is correct.

![image](https://github.com/gf500/Active-Directory/assets/121585575/dcf737ca-6d87-41a2-aedc-51d4148fee4f)

   - Click "Next" to skip WINS Servers as well.
   - We will be asked if we want to activate this scope now, we select "Yes" and click "Next".
   - Finally click "Finish" to finalize the configuration.

## Sites and Services

To better organize our network and ensure efficient communication between our computers and the appropriate Domain Controller, we will now set up Sites and Services. This configuration will help our computers in Montreal quickly find the right domain controller for tasks like authentication and accessing various services. It simplifies the process by eliminating the need to search through the entire network, making our network operations more efficient.

### Configuration
   - In Server Manager, under Tools select Active Directory Sites and Services.
   - Locate the existing "Default-First_site-Name" site and rename it Montreal-HQ.

![image](https://github.com/gf500/Active-Directory/assets/121585575/2964a667-4216-43c7-b2ef-20539fd5211c)

   - Still in the Sites and Services window locate the Subnets folder, right-click, and select "New Subnet...".

![image](https://github.com/gf500/Active-Directory/assets/121585575/067c2a86-c72f-465b-bd2d-87318ce80ed0)

   - Add our network address range for our Montreal-HQ, the same we gave in the DHCP section, which is 192.168.10.0/24.
   - Select the Montreal-HQ site under "Select a site object for this prefix." and click "OK".

We've now finished our basic setup for our HQ Domain Controller, we are now ready to move on the create OUs, Users Accounts and joining our workstation VM to the domain in the next part of our project.







