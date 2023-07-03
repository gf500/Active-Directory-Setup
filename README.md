# Active-Directory
Active Directory Home-Lab

This project demonstrates the setup and configuration of an Active Directory home-lab environment for ACME, a fictional company. The company's headquarters is located in Montreal with a branch office in Boston. By setting up this lab environment, we aim to showcase the implementation of an AD infrastructure that reflects the organizational structure of a large-scale company. This readme provides an overview of the project and guides you through the steps to recreate the lab setup and configuration.

## Lab Environment Setup
For our starting environment, we're using VMware Workstation to create a virtualized setup for our Active Directory home-lab. We've set up two virtual machines to simulate our network, we will be adding another VM for the Boston Domain Controller later on.


Windows Server 2022 VM:
- This VM acts as the domain controller for the Montreal headquarters, managing user accounts, group policies, and other Active Directory services.

Windows 10 Workstation VM:
- This VM represents a regular workstation within the ACME network. It runs Windows 10 and allows us to test and demonstrate the features of our Active Directory infrastructure.


## Domain Controller

We will first change our server hostname and then promote our Windows Server VM to a domain controller and create our acme.com forest:

1. Change Hostname:
   - In the Windows Server VM settings, click on "Rename this PC."
   - Rename our machine to SRV-MTLDC01, where SRV stands for Server, MTL represents Montreal, DC represents Domain Controller, and 01 is the server number.
   - Restart the VM for the changes to take effect.

2. Promoting the server to Domain Controller:
    

Promoting the server to Domain Controller:
- In Server Manager, click on "Add roles and features", in the Server Roles tab select Active Directory Domain Services.
- We keep features as is and Next, we will click “Next” and “Install” to finish up the ADDS install. 
- In Server Manager, in the top right corner we now have a new yellow triangle, clicking on this, we see a pending Post-deployment Configuration option called “Promote this server to a Domain Controller”.
- Select "Promote this server to a domain controller".
- Choose the option to add a new forest since we are starting from scratch and enter the root domain name as acme.com.

![image](https://github.com/gf500/Active-Directory/assets/121585575/015735d6-0afa-4020-8acb-db2543e39579)

- Click Next, we can leave the options as-is, add a DSRM password and click Next again.

![image](https://github.com/gf500/Active-Directory/assets/121585575/9180c4a6-bb25-4008-94a2-e9b82b090f5c)

- We will leave all remaining options as-is and continue clicking Next until Install.
- Once the installation completed you will be brought back to the login screen.

We now have a SRV-MTLDC01 serving as the domain controller for the acme.com root domain.

