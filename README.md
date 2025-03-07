<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Installing Active Directory Deployed in the Cloud (Azure)</h1>
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
- Using Remote Desktop: Install and Deploy Active Directory

<h2>Deployment and Configuration Steps</h2>

<h2>Step 1- Using Azure: Setup the Domain Controller</h2> 
<p> 
  
![image](https://github.com/user-attachments/assets/52066ae9-5fcd-4e71-aba0-8aa94fadace3)

</p>
<p>
We need to create a resource group which will act as our folder that organizes and manages all of our information. Click the search bar to navigate to resource groups by typing “resource groups” or click the box highlighted on the main page. When you click resource groups there will be a “+ create” button in the left corner of the page. <strong>Click it!</strong>
<strong>Note:</strong> Under the resource group you will always find your virtual network, virtual machines, their public IP addresses, their network security groups(NSGs)  and more! This also means that when you delete a resource group, <strong>EVERYTHING</strong> in it will be deleted without recovery.

</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/efafea76-d68e-41dd-afa4-3ebdfeedf949)

</p>
<p>
<h3>Creating Our Resource Group:</h3>
  Label the resource group “Active-Directory-Lab” and your region “East U.S 2”. Now you can click “review + create” and create your virtual machine. Yay!

  <strong>Note:</strong> The region you select for your resource must be consistent with the rest of your creations, such as your virtual network and virtual machines (VMs). Your virtual machines must all be on the same network to communicate in this lab, and it's detrimental to the success of the rest of this activity that they can be pinged(show connectivity between one another). I will provide further troubleshooting later, in case the region doesn't let you access the proper image you need for the Client VM.

</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/837a3650-e78f-4fd7-a800-a6de4076da59)

</p>
<p>
<h3>Creating Our Virtual Network (VNet):</h3> 
  To get started creating your <strong>virtual network(VNet)</strong>, navigate the search bar just as you did with the resource group or use the home page. Then, click “+ create”.  Now make sure your resource group is “Active-Directory-Lab”, label the virtual network “Active-Directory-VNet”,  and the region “East US 2”. 
<strong>Note:</strong> Be sure you’re clicking the <strong>VNet</strong>, not “virtual machines” because I made that mistake my first time. It’s not hard to fix, just delete it/click out(if it’s not yet created) and go back to VNet.

</p>
<br /> 


<p>

  ![image](https://github.com/user-attachments/assets/976775e8-18f6-4f51-b518-c84e2554a21c)

</p>
<p>
Bypass the other pages without clicking or changing anything and click “create”.
</p>
<br /> 

<p>

  ![image](https://github.com/user-attachments/assets/382268c0-0508-4d31-a8c4-7b48633c5d4e)

</p>
<p>
<h3>Creating VM “DC-1”:</h3>
  To create our first virtual machine, repeat the first steps from our previous creations to locate and state creating. Then, after confirming the resource group is “Active-Directory-Lab”, name the VM “DC-1”. Again, the DC is our domain controller which will be the brain of AD allowing it to function and manage our client. **Remember**, it is important that your region is “East US 2” so that it works on the virtual network we finished previously. 

<p>

  ![image](https://github.com/user-attachments/assets/b610cb49-f31f-47d9-99ee-38d88ec75c64)

</p>
<p>

  For our “image” select “Windows Server 2022 Datacenter: Azure Edition- x64 Gen2”.
Using Windows Server is detrimental because the DC needs to be managed by AD DS(Active Directory Domain Services) to use Windows Server features like Group Policy and access control for Active Directory. AD DS controls and manages our DC which controls AD (ADDS<DC<AD).  Now select a standard size of at least 2 vCPU. Virtual CPUs pull from your CPU’s core to power your virtual machines. Hypothetically you can use 1 vCPU per virtual machine but using 2 makes ours less likely to run slow while running our testing because of the extra power. Fill in as follows (Username: Labuser / Password: Cyberwiz3456). 

<strong>Note:</strong> If you’re required to change your region to accept the size and/or image necessary, find the acceptable region and exit the screen without creating the VM. You need to delete your resource group and virtual network to create new ones using the region accepted for your VMs. However if you are required to change “zones” to satisfy the size and image then change the zone. A zone is just an area within the network so VMs are allowed to be on the same one or different ones. The redundancy is allowed in case of failures in other zones.

</p>
<br /> 

<p>

  ![image](https://github.com/user-attachments/assets/2de4ed53-2401-4895-a564-f18001b2396b)

</p>
<p>
Lastly, check the confirmation boxes below to move on to disks and then “networking”. 
</p>
<br /> 

<p>

  ![image](https://github.com/user-attachments/assets/84ad396f-0469-4c86-a690-20922d07576d)

</p>
<br /> 
Confirm that “Active-Directory-VNet” is selected as the virtual network of our virtual machine. Now click “review + create” and create it!

</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/4874c427-4240-48e9-84f1-e9f44d26e51e)

</p>
<p>
<h3>Making DC-1’s NIC Private IP Address Static:</h3>
  We need to make DC-1’s Private IP address static because it has a dynamic Private IP by default, meaning that it can be likely to change. If the IP changes, then Client-1 will no longer be able to use DC-1 as its DNS server once we create Client-1’s DNS server to use DC-1’s Private IP address. Go to DC-1’s networking settings and click the NIC box. 

<p>

  ![image](https://github.com/user-attachments/assets/80348c5e-9c0f-4337-b74e-a615191dcc2e)

</p>
<p>
On the following page click “ipconfig1” and select “static” on the pop-up. Next click save to complete the change.

</p>
<br /> 
<h1>Step 2- Using Remote Desktop to Complete Setup</h1> 
<h3>Disabling the Firewall (To Test Connectivity between both VMs)</h3>
 
 <p>

   ![image](https://github.com/user-attachments/assets/5e8e5e34-402e-46fa-befd-1d24fa13f725)

</p>
<p>
Go under “Virtual Machines” in Azure and copy and paste the Public IP address from DC-1. We will use this to connect to it via Remote Desktop so that we can disable the firewall to test for connectivity between both VMs later in the lab from Client-1.

</p>
<br /> 

<p>

  ![image](https://github.com/user-attachments/assets/e080c531-9bfc-4523-aa7d-fda58532c077)

</p>
<p>
Paste the Public IP address and when you are prompted, give the username and password we set for DC-1 previously while we were creating it. The username was “Labuser” and the password was “Cyberwiz3456”. Now click connect and say “yes” to the prompt asking if you’re sure you want to connect.

  <strong>Note:</strong> Ensure that if you are on a Windows PC that you are not logging in with your personal account to the VM. This will trigger your login not to work. Click “use another account” if you run into this, and if everything else goes through, don’t worry about anything. 
</p>
<br /> 

<p>

  ![image](https://github.com/user-attachments/assets/cc96ecb9-ab84-42c0-8b39-dfacae63bf30)
  ![image](https://github.com/user-attachments/assets/849f2062-de30-4228-ba83-004dc0aa8189)

</p>
<p>
This is what your opening screen should look like when you are in the DC-1 and it’s a server. Also to double check, right click the “start” button (the 4 windows) and click “system”. The page that appears will show the properties of the VM such as the edition. It should say “Windows 2023 Datacenter Azure Edition).

</p>
<br /> 

<p>

  ![image](https://github.com/user-attachments/assets/ac938f72-a44d-48b6-a23d-d7f6f28a9fb2)

</p>
<p>
Right click the “start” menu again, select “run”, type “wf.msc” for Windows Firewall, and click “okay”. 

</p>
<br /> 

<p>

  ![image](https://github.com/user-attachments/assets/47011982-dec6-40b3-b40f-8d1ad272a2d7)

</p>
<p>
Secondly, select “Windows Defender Firewall Properties”. Under “domain profile” select “off” for “firewall state”, “private profile” and “public profile”. Lastly, click “apply” and “okay”. Now we can move on to the next half of this configuration.

</p>
<br /> 
<h1>Step 3- Using Azure: Setup Client-1 </h1>
<h3>Creating VM: Client-1 </h3>
<p>

  ![image](https://github.com/user-attachments/assets/59ca9287-57e6-485f-84d4-151298407515)

</p>
<p>
Locate “virtual machines” and click “create”. Label the VM “Client-1”  and put it in “East US 2” or your correlating region. Our Client-1 VM will act as our computer being controlled and managed for our company with Active directory. This is why when we finish configuring it we will test connectivity between Client-1 and DC-1. As you may have noticed, I had to change my zone for Client-1 to zone 3 in order to satisfy the necessary size and picture. Again, it's perfectly okay to organize them this way.

</p>
<br /> 

<p>

  ![image](https://github.com/user-attachments/assets/a045014c-8062-4223-af25-d929e2b3186a)

</p>
<p>
Make the image “Windows 10 Pro, version 22h2 x64 Gen2” and make sure the size you select has at least 2 vCPUs with 8 GiB or RAM. Now as before, use the username and password (Labuser, Cyberwiz3456) and check the necessary confirmation boxes at the bottom. 
</p>
<br /> 

<p>

  ![image](https://github.com/user-attachments/assets/a9fcf27f-d707-4439-adb9-0e789bfa8efd)
  ![image](https://github.com/user-attachments/assets/53aa1454-3983-4480-a3e0-d77ec2ce78a5)

</p>
<p>
Click through the tabs until you reach networking to confirm “Active-Directory_VNet” as your virtual network and create Client-1.

</p>
<br /> 

<h3>Changing Client-1s DNS Settings to DC-1s Private IP Address</h3>
Doing this will cause DC-1 to act as the DNS system for Client-1 so that when it is searching for information it needs, it will only use the Active Directory system in DC-1. 

  <p>

  ![image](https://github.com/user-attachments/assets/b85fdfc5-88b7-4065-9a57-2920ac0e448d)

</p>
<p>
Go back under DC-1 and copy it’s Private IP address. 

</p>
<br /> 

<p>

  ![image](https://github.com/user-attachments/assets/cace1d47-19c7-4966-b25e-084c78a509ea)

</p>
<p>
Then go under Client-1, go back into the NIC, and click “DNS servers” on the left panel.
Now the original DNS server is coming from your VNet, but we are going to click “custom” and paste DC-1’s Private IP address in order to redirect it. This will mean your DNS servers come from DC-1 instead as its specialized database.Now we can join the domain. For example, whenever Client-1 is looking for Disney+.com it will use DC-1 as its DNS, acting like a GPS for finding the IP address to Disney plus, and  telling the computer where to go to get there. Restart Client-1 so everything is in, log into it using the Public IP address, and the same username/password.

</p>
<br /> 

<h1>Step 4- Using Remote Desktop: Complete Client-1 Setup and Testing Connectivity.</h1>
<h3>Pinging DC-1 From Client-1</h3> 
<p>

  ![image](https://github.com/user-attachments/assets/041f2861-458d-4575-8bde-6f6ec3b41cae)

</p>
<p>
Once logged in, right click the “start” button and select “Windows Powershell”. You can also search and select it in the search area on the taskbar. Now we are going to ping DC-1’s Private IP address. Mine is (10.0.0.4) and yours can be found as before under the information screen for the VM.

</p> 
<br /> 

<p>

  ![image](https://github.com/user-attachments/assets/ef401923-9201-4cbe-b055-65c4b252e519)

</p>
<p>
Type ping and DC-1’s Private Ip address into the command line and observe what happens. As you can see DC-1 has made replies to the ping from Client-1 in response verifying their connection. Ping is a tool that uses Internet Control Message Protocol (ICMP) to test the connectivity between devices on a network. This is what a successful ping looks like. 

</p>
<br /> 
<h3>Pinging Output for Client-1s  DNS Settings </h3>
<p>

  ![image](https://github.com/user-attachments/assets/f148790e-6a77-4426-b568-e9bc03759850)

</p>
<p>
We are going to run “ipconfig /all” because it will tell us all of the details about the computer and its network makeup information like its Private IP address and subnet mask or DNS servers. We are using it to confirm its using DC-1 as its DNS server by looking for DC-1’s Private IP address.  Make sure that the slash is with “all” and not “ipconfig” or it will populate a red failure because it isn't formatted correctly. Observing the information populated, we see the host for the connection is Client-1. In addition, when you look down to DNS servers we see that DC-1’s Private IP address is populating. We can conclude that Client-1 one is successfully using DC-1 as its DNS server 

</p>
<br /> 

<h1>Step 5:Using Remote Desktop: Install and Deploy Active Directory</h1>

<p>

  ![image](https://github.com/user-attachments/assets/6acfe3b3-1310-49c0-bb3d-8ea7a05290fe)

</p>
<p>
While still in the domain controller open Server Manager, then select "add roles and features". Next select "Server Roles", check the box labled "Active Directory Domain Services".
</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/aa019733-2593-4070-a7a6-e6be65d7f8a0)

</p>
<p>
Select "Add Features".
</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/0b70b983-e940-4ce5-8465-64bf9cebac8c)

</p>
<p>
Select "install" and choose "yes" to restarting the virtual machine if neccessary.
</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/76990efa-e256-410c-861c-ccd67bf618be)

</p>
<p>
Once the ADDS roles and features have been installed you will select the flag in the right corner of the Server Manager. It is marked by a hazard looking sign. Select "promote this server to a domain controller". The next pop-up will say "deployment configuration". On that first page select "add new forest" and type "mydomain.com" as the root domain name. Click next to "DNS Options".
</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/707ff904-e32b-44ff-8425-6ec39891bfcb)

</p>
<p>
Deselect the option to create a DNS delegation. Keep clicking next until the prerequisites check is running. Click next to installaton.
</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/5d5d597c-c9aa-4f0e-aacb-8a99e109adf5)

</p>
<p>
Run the install and let the DC-1 VM automatically restart.
</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/b05e92bc-011a-4abb-8d01-da3c6c386d66)

</p>
<p>
Because DC-1 is the Domain and has been successfully deployed we will need to login under our specific domain. We want to specify our login apart from a local admin. Log back into DC-1 as "mydomain.com\ur username". Mine would be "mydomain.com\Labuser", using the same password "Cyberwiz3456". Click the start button (4 windows), click "Windows Administarative Tools", and open "Active Directory Users and Computers (ADUC)".

</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/19de9cf9-76f2-4a03-81f1-f9c0d4400a4d)
  ![image](https://github.com/user-attachments/assets/f54fbba4-d7d7-468b-87d9-ff30e015ae28)
  ![image](https://github.com/user-attachments/assets/14d72981-cc55-4f89-b138-4b6b4bca403c)

</p>
<p>
<strong>Creating OU's:</strong> Right click "mydomain.com", click "new" to create the organizational units <strong>"_EMPLOYEES"</strong> and <strong>"_ADMINS"</strong>.
  
  <strong>Note:</strong> It's imperative to label these groups exactly how they are shown for the future when, for example, adding an admin which I will demonstarte here.
</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/c9ae35c6-0ce4-4b0d-88a4-e1d52578f7e0)

</p>
<p>
<strong>Creating An Admin User:</strong> First, right click "_ADMINS", click "new" and "user". 
<br />

<p>

  ![image](https://github.com/user-attachments/assets/33cb7a24-402d-4c65-938c-d08388c5a825)

</p>
<p>
</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/32d40108-19e7-4f05-8613-467a5cae3530)
  ![image](https://github.com/user-attachments/assets/e21a3c17-0c9e-4277-92d0-5607bea65caf)

</p>
<p>
Make her password "Cyberwiz3456". Then deselect "user must change password at next login" and select that the passowrd never expires if you'd like. This isn't something you would do in real life but may be used for the conveinience of modeling it here for all intents and purposes.
  </p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/6629d9e4-0e05-416e-9f25-1989673c6f28)
  ![image](https://github.com/user-attachments/assets/81ccb8ad-eb90-44b4-8d22-9f3690776a61)

</p>
<p>
Under "_ADMINS" right click "jane_admin" and go to her user properties. Navigate to "member of" and add her to the Domain Admins security group. After adding it, type "domain admins" and check the name. Next, confirm everthing by selecting apply. Finally, log out and log back in as "mydomain.com\jane_admin". This will be the admin account I use for the rest of the demonstration.

  <strong>Note:</strong> Use the proper slash! In the screeshot, at the very bottom, I  accidently put the wrong slash initally but corrected it with the proper slash (\) at the top. The typo populates a double "mydomain.com".
</p>
<br />

<strong>Joining Client-1 to the Domain: "mydomain.com"</strong> 

<p>

  ![image](https://github.com/user-attachments/assets/7ba18850-3e97-4953-8a3f-172ad8f554e5)

</p>
<p>
Login to Client-1 as "Labuser" and use the same password from previous logins (Cyberwiz3456).
</p>
<br />

<p>

  ![image](https://github.com/user-attachments/assets/f4b41d75-0ec9-4703-ac4a-2a1f39569200)
  ![image](https://github.com/user-attachments/assets/634eb651-bf5b-40c5-a6e6-8b7c6274c02f)
  ![image](https://github.com/user-attachments/assets/a9789d70-a9f0-464f-94e8-38658de27576)
  ![image](https://github.com/user-attachments/assets/432d460d-0c28-4345-89d4-ba02addb6f34)

</p>
<p>
1. Go Under the computer's system and select "Rename this PC (advanced).
2. Make Client-1 a member of "mydomain.com" under "domain".
3. Enter Jane Does name and password(Cyberwiz3456) because she is an admin, so she will have permissions to access to the domain. 
</p>
<br />

<strong>Confirm Client-1 has been Successfully Added</strong> 
<p>

  ![image](https://github.com/user-attachments/assets/ceefe776-3e41-4e19-8b17-3ed040b833bc)

</p>
<p>
1. Under DC-1 as "mydomain.com\jane_admin" go back to Active Directory Users and Computers.
2. Go to "Computers" under the domain and observe that Client-1 has succefully be added as a computer on the domain.I also observed the properties of the computer there too. 
  
</p>
<br />

<strong>Creating a "_CLIENTS" Folder </strong> 
<p>

  ![image](https://github.com/user-attachments/assets/88f1b3af-a7cf-4305-aeb6-00f2d9a99fb9)
  ![image](https://github.com/user-attachments/assets/2fa1adcb-919a-4be3-af02-4e47d135bd23)
  ![image](https://github.com/user-attachments/assets/ee504a7b-14c0-457e-908d-bae2fa2c8b29)

</p>
<p>
1. Create "_CLIENTS" under OU's for future clients to be oorganized here.
2. Under "computers" move Client-1 to the "_CLIENTS" folder and confirm this.
3. Now right click "mydomain.com" to refresh the folder and see eveything organized.
</p>
<br />

<h1>Conclusion/ Clean Up </h1>

This concludes the walkthrough for installing and deploying Active Directory in Azure. If you plan on continuing to use these VM’s further to add useres and paratice troubleshooting, then go back to Azure and stop them from running. If you’re completely finished, delete the resource group we created to delete everything else as well. I have shown this below. Remember Azure is a pay as you go, so you are charged for resources, including space, that you choose to take up. Forgetting to stop VMs from running or deleting unused resources will result in charges to your card filed onto your account or quickly diminishing credits if you’re still on your free trial.
<p>

  ![image](https://github.com/user-attachments/assets/3ddef873-42a6-4443-9d3a-6c27064fb486)
  ![image](https://github.com/user-attachments/assets/106fb1c8-a134-4cd3-b5af-621ba43c047d)


</p>
<p>







