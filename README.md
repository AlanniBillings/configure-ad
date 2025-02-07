<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Configuring Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>High-Level Introductory Overview</h2>

- Active Directory (AD) is a software built by Microsoft. It acts as the hive mind for businesses controlling accounts, passwords, permissions and much more. It even allows us to control devices on a large scale to do these things. For example, you’re an administrative associate in your I.T department and a new update hits all 26 department computers making it easier to work the ticketing system. Should there be a bug involved, your position is recognized in the AD system giving you permission to remotely uninstall the update from every dept computer. Here, AD played a huge part in the initial installation of the update, and the admin being allowed to uninstall it remotely for the entire department. Here, I will walk through using Azure to configure a domain controller (controls AD)  and a 1 client for it to work on using Active Directory.


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines and Computing)
- Remote Desktop (To Access and Compute in VMs)
- Active Directory Domain Services (AD DS)
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Using Azure: Setup Domain Controller 
- Using Remote Desktop: Log into DC-1  to Complete Setup
- Using Azure: Setup Client-1
- Using Remote Desktop: Open Client-1 to Test Connectivity to DC-1 

<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br /> 
