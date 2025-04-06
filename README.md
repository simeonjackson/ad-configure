<h1>Configuring Active Directory</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines. <br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows 11
- Windows Server 2022

<h2>Deployment and Configuration Steps</h2>

<p>
Active Directory allows a company to centrally manage thousands of user accounts in a single place. Administrators are able to control permissions, access controls and provide resources at a large scale while enhancing productivity and security.

In this lab I created an Active Directory domain using Windows Server as my domain controller as well as a client VM. I then added a few users representing a company giving each user access to the client machine with varying levels of permissions. This represents how an enterprise might use Active Directory in their business.

I created a resource group which housed my virtual machines for this lab. I used Windows Server 2022 for my domain controller (DC1) and I used Windows 11 as my client VM (Client 1) making sure both were on the same virtual network and subnet.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
In order to use DC1 as a domain controller for this network, I gave it a static IP address. This is because my Client VMs on this network will use DC1 as a DNS server to reach outside the network. If this address was dynamic there is a possibility that the IP address changes and my client VM would be unable to find DC1.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
On Client 1, I changed the DNS server to be the private IP address we just set on DC1 via Network Settings in Azure. This will allow us to join DC1's domain.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
Logging in to Client 1, I sent a ping to DC1's private IP address in order to verify that both machines are in the same network. Running ipconfig /all will also show that the DNS server is the private IP of DC1.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
Now that the network is configured I set up Active Directory on DC1. Using Server Manager, I installed Active Directory Domain Services (ADDS) onto DC1.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
Back in Server Manager I promoted the server to a domain controller. I gave my domain the root name mydomain.com and completed the installation of ADDS.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>


<p>
Now that DC1 is a domain controller you must specify when logging in the domain being used and the user who is logging in. Using "User@Domain" or "Domain\User" as a username.

In this lab instance for me that is mydomain.com\SimeonLab followed by the password that you set.

</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
Now within the domain I created an Admin user as well as organizational units (OU) that will help me organize my employees.

In Active Directory Users and Computers (ADUC), I created new OUs and I made two, "Employees" and "Admins", I also manually created a user for the admin group which I later set up as an admin for our network.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
Clicking on my admin member, "Admin User", I can add this user to the group "Domain Admins" which will actually make the user an administrative user. This will allow Admin to make create users and set permissions within the domain.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
Client 1 still has not been added to our new domain. To do so, I switched to my client VM and joined the domain via the Rename my PC setting on the system about page. All I needed to do was add my domain name and enter the credentials of our new admin to add the client. This is because we set up Client 1 to point to DC1 as its DNS server before.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
Back on DC1 we can see that Client 1 shows up in ADUC in the Computers folder.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
Now that Client 1 is on the domain we can set it up so that our domain users can access it via remote desktop. On Client 1 I clicked remote desktop in system properties and I allow "Domain Users" to have access.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
In another lab I created a PowerShell script to create employees in the "Employees" OU. These employees should be assigned to the "domain users" group and I should be able to log-in to client 1 with them. I chose a name from the list and attempted to log-in.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
Once in I can verify that I am logged in as Abigail Smith in the Terminal. This account only has domain user access which I verified by trying to access an admin folder. 
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
These are a few of the basics I learned using Active Directory, I have another lab where I looked at Group Policy which is a way of applying settings across our entire network such as password policies and security settings.
</p>
