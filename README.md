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

- Setup Resources in Azure
- Ensure Connectivity between the client and Domain Controler 
- Install Active Directory 
- Create an Admin and Normal User Account in AD
- Join Client-1 to your domain(mydomain.com)
- Setup Remote Desktop for non-administrative users on Client-1
- Create a bunch of users and attempt to log into Client-1 with one of the users 

<h2>Deployment and Configuration Steps</h2>

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/15685209-6740-413f-85f4-5f7d3bbacfc0)

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/647db4c7-7066-425d-89f0-d7f06cdfb604)



<p>
First, we need to set up our resources in Azure by creating the Domain Controller which will be tagged as Dc-1, and the client computer which would be client-1. 

NOTE; When creating a virtual machine, Azure will automatically create a virtual network for the domain controller. Also, make sure to create a new resource group(AD-Lab) 

</p>
<br />

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/fc8fe5aa-3888-4e4c-b731-d1cd99240ee5)

<p>
Now by default, the DC-1 will have a dynamic IP address, since the Domain controller will be assigning IP addresses to client computers we need to change the DC-1 IP to Static. This can be done in the IP configuration tab in Azure
</p>
<br />

<p>
Now we must ensure that we are able to communicate with our Domain Controller from our Client machine. Log into DC1 and type in the start menu mf.msc to open up the firewall settings. Filter by protocol and enable all the IPv4 rules. This will open up the firewall to ICMP traffic. (Note the first screenshot is showing the failed attempts and the next is showing what happens after we try to ping the DC.
</p>

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/0bc09f50-8019-4d4e-96f5-defa371f5350)

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/9e53b9ff-d4b2-4016-a25f-965a5a231f8a)

Now that we are allowing ICMP traffic let's try to ping the computer again.

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/a34e23b6-19c8-4b13-b524-c98054a98e78)


<br />

<p>
Now go to DC1 and install AD. Click on Add Roles and Features. Make sure you check Active Directory Domain Services and proceed with the installation by clicking next. 
</p>
<br />

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/b31ef22d-6794-4673-a5dc-f78cec998b22)

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/0d138045-e4f5-4b13-aaa2-e4cd25c43475)

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/d3329670-a87f-4f82-9411-b76c5f068d51)


<p>
Click on Add a new forest and this is where you are going to set your domain. It can be anything that you want for now we are going to set it as mydomain.com 
</p>
<br />


STEP 4
- 4.1 In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”
- 4.2 Create a new OU named “_ADMINS”
- 4.3 Create a new employee named “Jane Doe” (same password) with the username of “jane_admin”
- 4.4 Add jane_admin to the “Domain Admins” Security Group
- 4.5 Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”
- 4.6 User jane_admin as your admin account from now on

<br />

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/9ceb8a71-fc47-48cd-8a70-ae99878d2eab)

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/1b079674-3b5a-4b34-ba48-64779cf7cc4e)

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/cddb6572-95ef-446e-8a66-64887f7caaac)


<p>
The next step is to join Client-1 to the domain(my domain.com)

From the Azure portal set client 1 DNS settings to the Dc’s Private IP address
NOTE: The current state of the Client 1 DNS is automatically pointing to the VNET DNS Server(Azure virtual DNS) we need the client to get its DNS from the domain controller. So if we try to connect to the domain it is reaching out to the VNET asking if it "has" mydomain.com resulting in an error.
</p>

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/f8462acd-9ad4-496c-ad1c-af79dfbdbf2a)


<br />

<br />

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/6efa5a94-aa91-4d23-966e-c2c7cdc36da8)

<br />

<p>
Click on Custom and paste the DC-1 private IP address 
</p>
<br />

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/3e96c968-1b45-490b-b573-22fad64aa796)

<p>
Now we are able to log into client 1 with our admin user(Jane_admin). We can verify this by opening up the command line, typing ipconfig/all, and seeing the DNS Server IP changed to that of the DC-1 private IP address.
</p>
<br />

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/ba88a529-4d2b-40aa-a3ff-cb7cb45a6117)

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/0b2bea32-7c50-4ba0-bc45-057f1c47cf81)

<br />

<p>
Login using your admin Jane account. 
</p>
<br />

<p>
Next, we are going to allow all domain users to Remote Desktop into client 1 machine
</p>
- Go to settings 
- Click Remote Desktop
- Under User accounts click “Select Users that can remotely access this PC”

<br />

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/18659b79-4937-4bd1-8be7-b64b998b11e2)



<p>
Now let’s create some users. Instead of creating users one by one which can be a very long process, we are going to use automation. Using a Powershell script we can create multiple users in one setting, this saves time and energy( The idea is to work smarter not harder :) ) 
</p>

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/20f4ed21-8e04-403a-b411-5f2509ce27c7)


<br />

<p>
Now we are able to log into any random user that was never logged into client 1 machine because they are now a part of mydomain.com  
</p>
<br />

![image](https://github.com/Algoroy27/configure-ad/assets/137920855/98b77fa7-35a9-48c0-8c8e-bb6bcccca842)

<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

