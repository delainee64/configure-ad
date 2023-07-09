# configure-ad

<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create two Virtual Machines within Azure:
- Domain Controller VM (Windows Server 2022) -- set Private IP Address to STATIC
- Client VM (Windows 10) -- using the same Resource Group and Vnet as DC
- Login to both VMs using Remote Desktop (RDP).
- Enable Inbound Rules for "Core Networking Diagnostics" within Domain Controller's Firewall to ensure connectivity between the Client and Domain Controller.
- Install Active Directory Domain Services within Domain Controller VM
- Create an Admin and Standard User Account in AD
- Link Client VM to a domain, then log in using the original admin account
- Setup Remote Desktop for non-administrative users on the Client VM
- Create additional users and attempt to log in to the Client VM as one of those newly created users

<h2>Deployment and Configuration Steps</h2>

<h3>&#9312; Create a Domain Controller VM</h3>
<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/092e777f-4868-4d6d-bdcd-9c31251d7ee3" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Go to "Virtual Machines" --> "Create" --> "Azure Virtual Machine".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/3cb45f4c-b6cb-4354-8660-248a47ea0d9d" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
- "Create" (name) a "New Resource Group" for your Virtual Machine (VM) to be stored in.
- Give your VM a name.
- Select a region, but keep this region consistent throughout the development of this Directory.
- Select "Windows Server 2022" as your base operating system.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/d22e6e7d-9bfc-4be3-b9a3-4dd8694c9f66" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
- Make sure to select at least two vcpus
- Create a username and password
- Select "Review + create" --> "Create"
</p>

<h3>&#9313; Create a Client VM</h3>
<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/92d91293-7c84-4ce4-94d8-3e25ee9df62a" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Create another VM. Place it under the same resource group and in the same region as the last VM. Give the VM a name and choose "Windows 10" as the base operating system.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/782d8617-7375-4537-b45e-32ef6e4fdd62" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Make sure at least two vcpus are selected. To keep things simple, use the same username and password as the previous VM and click "Next" until you reach "Networking".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/1a105177-1398-4315-8a0c-e29ce02fb861" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Ensure the "Virtual Network" is the same as the first VM we created and click "Review + create" --> "Create".
</p>

<h3>&#9314; Assign Domain Controller's Private IP address to STATIC</h3>
<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/3dfcbeb5-6fa5-493b-921a-f6b2e0dc0b32" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Go back to "Virtual Machines" and select the domain controller (DC-1).
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/5e71eb3d-c058-465b-9b60-748274f1b828" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Select "Networking" --> "Network Interface".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/898ae32e-b630-46ad-80ab-191696ce6327" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Select "IP configurations" --> "ipconfig1". We are going to change the status from "dynamic" to "static".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/9993c698-fb7d-4e74-8513-5b2650033fb5" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Select "Static" --> "Save".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/f358d3c4-e9db-43e2-9d62-0d6a3483e034" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Go back to Virtual Machines and select "Client-1". Copy Client-1's public IP address.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/925217cf-a752-434c-950e-4e8c91c92596" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Open "Remote Desktop Connection" on your computer and enter Client-1's IP address. Log in using the username and password you created earlier.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/16c82805-116b-44ba-b6b2-955098fcdd59" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Repeat this process, this time with DC-1's public IP address. Connect through "Remote Desktop Connection" and log in using the same username and password.
</p>

<h3>&#9315; Ensure Connectivity between the Client and Domain Controller</h3>
<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/6cccf1ae-61b4-4403-ae65-3b4990f29d7c" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
In DC-1, click on the Windows search bar and look for "Windows Defender Firewall with Advanced Security" --> "Inbound Rules" --> "Protocol" (for sorting purposes).
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/792aea0e-2765-499c-bd11-7aca64514b92" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Right-click and enable the ICVMP4 traffic "Core Networking Diagnostics".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/14d0b669-5875-4304-84d9-ff684939825a" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Go back to DC-1 in Azure and copy DC-1's private IP address.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/0cddb33d-b024-4cc1-8372-ad9271135788" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Go to Client-1's VM and open "Command Prompt". Ping DM-1's private IP address. If there is a good connection, we will receive four replies. 
</p>

<h3>&#9316; Install Active Directory Domain Services within Domain Controller VM</h3>
<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/661597a0-4437-4a88-aab4-648421b198c4" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Go to DC-1's VM and make sure "Server Manager" is open. Click "Add Roles and Features" --> "Next" until you reach "Server Roles" and then select "Active Directory Domain Services"--> "Add Features" --> "Next" until you have the option to "Install".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/7740d159-0505-4a7e-a434-49899f02d9a9" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Click on the caution sign and select "Promote this server to a domain controller."
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/687381ee-b1ce-4392-989c-75af2a8cbd20" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Select "Add a New Forest" and create a domain name. Select "Next" and create a password. Select "Next" until you are able to install.
</p>

<h3>&#9317; Create an Admin Account in Active Directory</h3>
<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/71155e8a-2dbc-4e3c-8053-793b8972e41b" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Once installed, you will be logged out of DC-1 VM. Log back into DC-1 as the domain controller through "Remote Desktop Connection". 
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/f45288d8-4ef1-40d8-aa8d-13c0498fed41" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Open "Server Manager" --> "Tools" --> "Active Directory Users and Computers".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/ffa691bc-0496-475b-aeed-2c6c2322114c" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Right-click "mydomain.com" --> "New" --> "Organizational Unit" and name it "_EMPLOYEES". Repeat this process to creat another organizational unit called "_ADMINS".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/734cfb88-47ab-45bd-b721-d5f81a2ca3f8" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Right-click inside "_ADMINS" folder and select "New" --> "User".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/a44aa3d2-a568-44a7-8c0c-e7360fceefe8" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Create a new admin account, select "Next" and set a password for the new account.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/da9e0982-2df2-4269-b4f8-be936d5ab3ab" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Right-click the new user and select "Properties".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/8a925db8-be27-42f5-8092-ce72fdeb9b09" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Select "Member of" --> "Add" and type "domain" --> "Check Name" and select "Domain Admins" --> "Okay" --> "Apply". Log out of DC-1.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/7e8c751f-80f2-469f-9ba5-cfabedf8d0e4" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Log back in with DC-1's public IP address. Log in using your DC admin credentials that we just created.
</p>

<h3>&#9318; Join Client-01 to the domain (mydomain.com)</h3>
<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/e92fbaad-993f-4379-8d3e-d2233587a894" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Go to your Azure Portal, and select your DC-1 VM. Copy the prive IP address.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/d72883a4-6824-4c9e-b15b-54498fb48dd3" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Go to VM Client-1 --> "Networking" and select Client-s's "Network Interface".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/e39de1f4-bfa4-48bb-bffa-499388442e45" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Select "DNS Servers" -- > "Custom" and paste DC-1's private IP address and hit "Save". Go back to your VMs and select Client-1 again.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/6dd91e8a-147f-4d6c-a1e4-6c0016259ca2" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Click "Restart" and copy Client-1's public IP address.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/05175fe3-e221-4c9f-80ab-16b93018dd6c" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Open "Remote Desktop Connection" and enter Client-1's public IP address. Log in with Client-1's credentials.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/724e5f81-db2f-4a1e-9b1a-ccfc949b4dfb)" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Go to the system's settings and select "Rename this PC advanced".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/6ec36da2-32e6-4fd2-bace-ecd4620973ce" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Select "Change" --> "Domain" and type "mydomain.com" --> "Okay" and then enter DC-1's administrator's credentials. Client-1 will restart.
</p>

<h3>&#9319; Setup Remote Desktop for non-administrative users on Client-01</h3>
<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/ab161249-8de5-4b90-8217-2642c2cae0e6" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Log back in to Client-1 through "Remote Desktop Connection". Use DC-1's administrator's credentials.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/9ab87f12-1e04-4b19-90c0-2e8e6fd390b4" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Go to system setting and select "Remote Desktop".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/56feb16a-efdc-4802-bf38-cd3f84bca032" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Select "Select users that can remotely access this PC" --> "Add" and type "domain users" --> "Check Name" --> "Okay".
</p>

<h3>&#9320; Create additional users and attempt to log into Client-01 with one of them</h3>
<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/86a28a02-a68d-4d96-b262-74a71bf10def" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Go back to VM DC-1. Go to Start and type "Powershell". Right-click on "Windows Powershell ISE" and "Run as an Adminstrator".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/7ee8b99a-bf79-405c-97ef-faffdfa75768" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Click on "New Script" and paste a premade script that creates new user names and deposits them into our _EMPLOYEES file. Click "Run Script".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/fb6b0d6d-479b-4dba-be2c-ead0bed43efa" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Minimize the window and go back to "Server Manager" --> "Tools" --> "Active Directory Users and Computers".
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/d5763a0b-1614-4987-96d8-59d10e1a2837" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Select "mydomain" --> "_EMPLOYEES" and select any name you wish and copy the name.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/5ef2b956-fb52-41d6-8894-b466d997ffc0" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Log out of VM Client-1 and log back in using Client-1's public IP address. Use the name of the employee you copied from DC-1 to log in to VM Client-1.
</p>

<p>
<img src="https://github.com/delainee64/configure-ad/assets/114307952/4bb0518d-7d75-4c59-a4c5-112073e5a88e" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Here we have our new user we created. We can do the same with any of the users we created in DC-1. This lab is complete. :)
</p>
