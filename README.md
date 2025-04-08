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

![Screenshot 2025-02-26 232036](https://github.com/user-attachments/assets/b9c9b178-fbc6-4f9a-9b1e-6ac054ec14d5)


<p>
In order to use DC1 as a domain controller for this network, I gave it a static IP address. This is because my Client VMs on this network will use DC1 as a DNS server to reach outside the network. If this address was dynamic there is a possibility that the IP address changes and my client VM would be unable to find DC1.
</p>

![Screenshot 2025-02-26 232256](https://github.com/user-attachments/assets/62577bdf-4cc9-4292-9cc7-e078f64d059d)


<p>
On Client 1, I changed the DNS server to be the private IP address we just set on DC1 via Network Settings in Azure. This will allow us to join DC1's domain.
</p>

![Screenshot 2025-02-26 232804](https://github.com/user-attachments/assets/69de3cde-f3e4-4773-bc14-ecaddfb32184)


<p>
Logging in to Client 1, I sent a ping to DC1's private IP address in order to verify that both machines are in the same network. Running ipconfig /all will also show that the DNS server is the private IP of DC1.
</p>

![Screenshot 2025-02-26 233736](https://github.com/user-attachments/assets/e9262eb6-20e4-48f6-883b-8a77b875bfc8)


<p>
Now that the network is configured I set up Active Directory on DC1. Using Server Manager, I installed Active Directory Domain Services (ADDS) onto DC1.
</p>

![Screenshot 2025-02-27 213822](https://github.com/user-attachments/assets/3c0ec029-6714-4fb1-b438-b8a500b5bfdb)

![Screenshot 2025-02-27 214130](https://github.com/user-attachments/assets/edf85aba-529f-4f0c-891e-815000bfb40b)


<p>
Back in Server Manager I promoted the server to a domain controller. I gave my domain the root name mydomain.com and completed the installation of ADDS.
</p>

![Screenshot 2025-02-27 214344](https://github.com/user-attachments/assets/3a3efc21-2973-428e-bbac-b8a672b1e88e)



<p>
Now that DC1 is a domain controller you must specify when logging in the domain being used and the user who is logging in. Using "User@Domain" or "Domain\User" as a username.

In this lab instance for me that is mydomain.com\SimeonLab followed by the password that you set.

</p>

![Screenshot 2025-02-27 215416](https://github.com/user-attachments/assets/4a656c7a-b6ef-4d47-a067-cbf0e4f3c875)


<p>
Now within the domain I created an Admin user as well as organizational units (OU) that will help me organize my employees.

In Active Directory Users and Computers (ADUC), I created new OUs and I made two, "Employees" and "Admins", I also manually created a user for the admin group which I later set up as an admin for our network.
</p>

![Screenshot 2025-02-27 220032](https://github.com/user-attachments/assets/928869b8-af96-4f6d-8fc1-12689df13f48)


<p>
Clicking on my admin member, "Admin User", I can add this user to the group "Domain Admins" which will actually make the user an administrative user. This will allow Admin to make create users and set permissions within the domain.
</p>

![Screenshot 2025-02-27 220934](https://github.com/user-attachments/assets/d75b627f-420e-467f-b15f-b76deee897d7)


<p>
Client 1 still has not been added to our new domain. To do so, I switched to my client VM and joined the domain via the Rename my PC setting on the system about page. All I needed to do was add my domain name and enter the credentials of our new admin to add the client. This is because we set up Client 1 to point to DC1 as its DNS server before.
</p>

![Screenshot 2025-02-27 222441](https://github.com/user-attachments/assets/1e7c2f42-c242-42c0-9756-1747cee35bdb)


<p>
Back on DC1 we can see that Client 1 shows up in ADUC in the Computers folder.
</p>

![Screenshot 2025-02-27 222609](https://github.com/user-attachments/assets/abe4afab-84a4-4dfc-8718-c5dc9b44d558)


<p>
Now that Client 1 is on the domain we can set it up so that our domain users can access it via remote desktop. On Client 1 I clicked remote desktop in system properties and I allow "Domain Users" to have access.
</p>

![Screenshot 2025-02-27 223102](https://github.com/user-attachments/assets/a3e87ade-e8f2-4930-ac1a-0de0cc530c89)


<p>
In another lab I created a PowerShell script to create employees in the "Employees" OU. These employees should be assigned to the "domain users" group and I should be able to log-in to client 1 with them. I chose a name from the list and attempted to log-in.
</p>

![Screenshot 2025-02-27 224118](https://github.com/user-attachments/assets/33804ab3-81c4-4505-885f-6342f19232fc)


<p>
Once in I can verify that I am logged in as Abigail Smith in the Terminal. This account only has domain user access which I verified by trying to access an admin folder. 
</p>

![Screenshot 2025-02-27 224442](https://github.com/user-attachments/assets/12c3197b-4868-4c4d-98bb-7954980f98e7)


<p>
These are a few of the basics I learned using Active Directory, I have another lab where I looked at Group Policy which is a way of applying settings across our entire network such as password policies and security settings.
</p>
