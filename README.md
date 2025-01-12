<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-Premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within two Azure virtual machines.<br/>


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)



<h2>Deployment and Configuration Steps</h2>

<h3>Step 1: Setup Resources in Azure</h3>

- Create two virtual machines
	- The first virtual machine will be the Domain Controller
		- Name: DC-1
		- Image: Windows Server 2022
		- Take note of the virtual network (vNet) that is automatically created
       
<p align="center">
<img src="https://i.imgur.com/xejR2AT.png)" height="70%" width="70%" alt="Azure Free Account"/>
</p>
<p></p>


	- Set DC-1's Virtual Network Interface Card (vNIC) private IP address to be static
		- Go to DC-1's network settings
		- Select Networking
		- Select the link next to Network Interface
		- Select IP Configurations > ipconfig1
		- Change the assignment from dynamic to static 
			- This ensures DC-1's IP address will not change
	   
<p align="center">
<img src="https://i.imgur.com/vYhvqLD.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/hOhFw4h.png" height="70%" width="70%" alt="Azure Free Services"/>  <img src="https://i.imgur.com/zkoTX9C.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>
<p></p> <p></p>


	- The second virtual machine will be the Client
		- Name: Client-1
		- Image: Windows 10 Pro
		- Use the same resource group and vNet as DC-1

<p align="center">
<img src="https://i.imgur.com/dsEnQBn.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/tFiCp8H.png" height="70%" width="70%" alt="Azure Free Services"/> 


<h3>Step 2: Ensure Connectivity Between the Client and Domain Controller</h3>

- Login to Client-1 using Microsoft Remote Desktop
- Search for Command Prompt and open it
- Ping DC-1's private IP Address (for example, 10.1.0.4)
	- Type "ping -t 10.1.0.4" into the command-line interface
	- The ping request continually  times out due to the firewall settings
		- To fix this, we need to enable ICMPv4 on DC-1's local Windows firewall

<p align="center">
<img src="https://i.imgur.com/SGKm1xB.png" height="70%" width="70%" alt="Azure Free Account"/> 
	
- Login to DC-1 using Microsoft Remote Desktop
	- Start > Windows Administrative Tools > Windows Defender Firewall with Advanced Security > Inbound Rules
	- Sort the list by protocols
	- Find "Core Networking Diagnostics" and "ICMPv4" and enable these two inbound rules

<p align="center">
<img src="https://i.imgur.com/RPMdJ03.png" height="50%" width="50%" alt="Azure Free Account"/> <img src="https://i.imgur.com/pxHRlAx.png" height="80%" width="80%" alt="Azure Free Services"/>
</p>

- Log back into Client-1 and the command line will automatically begin pinging DC-1 successfully
    
<p align="center">
<img src="https://i.imgur.com/TTrlgrH.png" height="70%" width="70%" alt="Azure Free Account"/> 


<h3>Step 3: Install Active Directory</h3>

- Log back into DC-1
	- Open Server Manager
	- Select "Add Roles and Features" > Follow the prompts
	- At Server Roles, check "Active Directory Domain Services."
		- Ignore how the picture below already says "Installed"
	- Select Add Features > select Next
	- Complete the installation

<p align="center">
<img src="https://i.imgur.com/3pRK1ya.png" height="80%" width="80%" alt="Azure Free Account"/> <img src="https://i.imgur.com/8feZZvO.png" height="50%" width="50%" alt="Azure Free Services"/>
</p>

- At the top right of the Server Manager Dashboard, click on the flag
- Select "Promote This Server to a Domain Controller"

<p align="center">
<img src="https://i.imgur.com/o1slUbP.png" height="70%" width="70%" alt="Azure Free Account"/> 
	
 - Select "Add a New Forest"
 	- Root domain name: mydomain.com
- Select Next
- Create a password
- Select Next and follow the prompts
- Select Install to complete the installation


<p align="center">
<img src="https://i.imgur.com/2jVUdTp.png" height="70%" width="70%" alt="Azure Free Account"/> 
	
- DC-1 will automatically restart
- Log back into DC-1 as user: mydomain.com\labuser               

<p align="center">
<img src="https://i.imgur.com/K21OBCZ.png" height="70%" width="70%" alt="Azure Free Account"/> 
	


<h3>Step 4: Create an Admin and Normal User Account in Active Directory v1.15.8</h3>
     
- On DC-1, open Server Manager
- Click Tools at the top-right of the screen
- Select Active Directory Users and Computers

<p align="center">
<img src="https://i.imgur.com/4QLhQXw.png" height="70%" width="70%" alt="Azure Free Account"/> 
	
- Right-click mydomain.com > New > Select Oranizational Unit (OU)
- Create two OUs
	- Name the first "_EMPLOYEES"
	- Name the second "_ADMINS"
	
<p align="center">
<img src="https://i.imgur.com/3zqNzG5.png" height="70%" width="70%" alt="Azure Free Account"/> 
	
	
- Right-click mydomain.com and click Referesh to sort the new organizational units to the top
- Go to the _ADMINS OU
- Right-click the name of the OU > New > User
	- First/Last name: Jane Doe
	- User login name: jane_admin
	- Select Next
	- Create a password
	- Uncheck all boxes
	- Select Next and then select Finish
<p align="center">
<img src="https://i.imgur.com/BGlTquH.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/REWylnk.png" height="70%" width="70%" alt="Azure Free Account"/> 
	
- Go to the _ADMINS OU
- Right-click Jane Doe > select Properties
	- Click the tab named "Member of" > select Add
	- Type in the names of your domain administrators
	- Select "Check Names" > OK > Apply
- Log out of DC-1 as "labuser" and log back in as “mydomain.com\jane_admin”



<p align="center">
<img src="https://i.imgur.com/oZekONX.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/vGb8Kx8.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>
 
     

<h3>Step 5: Join Client-1 to your domain (mydomain.com)
</h3>

- Go back to the Azure portal
- Navigate to the Client-1 Virtual Machine
- On the left-hand side of the screen select Networking
- Select the link next to the NIC > select DNS Server > Custom
- Type in DC-1's private IP address
- Click Save
- After it is done updating, select Restart and select Yes

<p align="center">
<img src="https://i.imgur.com/6xNBs9u.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/7HM7vXT.png" height="70%" width="70%" alt="Azure Free Services"/>  <img src="https://i.imgur.com/kuvL4II.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>

- Log back into Client-1 using Microsoft Remote Desktop as the original local admin (labuser)
- Right-click the Start menu and select System
- On right-hand side of the screen, select Rename This PC (Advanced) > Change
- Under "Member of," select Domain
- Type "mydomain.com" and select OK
	- Username: mydomain.com\jane_admin
	- Type in password and press OK
- Restart the computer 			


<p align="center">
<img src="https://i.imgur.com/5ssccMG.png" height="80%" width="80%" alt="Azure Free Account"/> <img src="https://i.imgur.com/cmQQHm4.png" height="50%" width="50%" alt="Azure Free Services"/>
</p>

<h3>Step 6: Setup Remote Desktop for non-administrative users on Client-1
</h3>

- Log back into Client-1
- Use mydomain.com\jane_admin
- Right-click the Start menu and select System
- On the right-hand side of the screen, select Remote Desktop
- Under User Accounts, click "Select Users That Can Remotely Access This PC > select Add
- Type in the name of your domain users
- Select "Check Names" > OK > OK

 
 <p align="center">
<img src="https://i.imgur.com/9IVDuzI.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/HvgW4oh.png" height="60%" width="60%" alt="Azure Free Services"/>
</p>

<h3>Step 7: Create as many additional users as you would like and attempt to log into Client-1 with one of the users' profiles
</h3>

- Log back into DC-1 as jane_admin
- Search for "Powershell_ise,"
- Right-click on Powershell_ise and open it as an administrator
- At the top-left of the screen select New Script and paste the contents of the following script into it
	- You can find the script [here](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

<p align="center">
<img src="https://i.imgur.com/MSzldrO.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/ygcveCn.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>

- Click the green arrow button near the top-middle of the screen
	- This will run the script
- Once the users have been created, go back to Active Directory Users and Computers > mydomain.com > _EMPLOYEES
		- You will see all the accounts that were created
- You can now log into Client-1 with one of the accounts that were created
	- Try logging into Client-1 as user "bag.wamu" using the password "Password1"

<p align="center">
<img src="https://i.imgur.com/2RTZ96J.png" height="80%" width="80%" alt="Azure Free Account"/> <img src="https://i.imgur.com/kKGXAZV.png" height="50%" width="50%" alt="Azure Free Services"/>  <img src="https://i.imgur.com/lz2qROK.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>

🎉Congratulations! You have implementated on-premises Active Directory and created users within an Azure virtual machine!🎉
