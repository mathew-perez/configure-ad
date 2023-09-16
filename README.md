<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>How to Install and Use Active Directory</h1>
Active Directory is a product by Microsoft that is for domain network services. It is native to Windows Server and is a common tool used in Information Technology today. There is an on premises version and an Azure version. In this tutorial, Active Directory was installed on a virtual machine within Azure.  <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>Configuration Steps</h2>

- Step 1: Create resources
- Step 2: Establish connectivity to between Domain Controller and Client
- Step 3: Install Active Directory
- Step 4: Create an administrator and regular account in Active Directory
- Step 5: Join Client to the Domain Controller 
- Step 6: Setup Remote Desktop for non-administrative users to Client
- Step 7: Create users in Active Directory using Powershell script

<h3>Step 1: Create resources</h3>

The first step is to create to two Azure virtual machines. 

The first virtual machine is the domain controller (DC-1) and will run Windows Server 2022. A domain controller is the main server computer network that gives access to its domain resources and authenticates users that are connected to it. 

The second virtual machine is the client (Client-1) and will run Windows 10. The client is just any computer connected to the domain controller. 

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/62884693-1096-41e3-9c3a-fb89cfc4423f)
![image](https://github.com/mathew-perez/configure-ad/assets/144407220/f791b1f0-1f4d-469b-b714-6e5302489616)


Next is to ensure DC-1's private IP address is changed from dynamic to static. DC-1's private IP address needs to be static so it does not change during the course of this exercise. 

To do this, go to DC-1's "Networking" section and click on the virtual Network Interface Card (NIC)
Click on IP configurations.

Click ipconfig1 for the private IP address (10.0.0.4) of your VM.

Click the dynamic to static option on the right drop down menu.

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/e54f59e6-1ebf-48ed-8c88-b60700d196af)
![image](https://github.com/mathew-perez/configure-ad/assets/144407220/6307588f-0914-4193-8dcc-1cd29bed91d1)


<h3>Step 2: Establish connectivity to between Domain Controller and Client</h3>
The second step is establish connectivity between Client-1 and DC-1. Use Microsoft Remote Desktop to connect to both virtual machines. 
<br>
Open Client-1 and open the Command line. Type in the command "ping -t" + the private IP address of DC-1. In this case, it is 10.0.0.4. The "ping -t" command will continously ping DC-1. 

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/19b1fe2f-9d6c-49c7-93fa-7d1bf78019f4)


Due to the firewall blocking incoming Internet Control Message Protocol (ICMP) traffic, the request is timing out. To fix this, login into DC-1 and open the application Windows Defender Firewall with Advanced Security (wf.msc) from the Start menu. From here, go to inbound security rules and sort the list by protocol. Scroll down to ICMPv4 and enable the two core network inbound rules. 

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/7e8c25db-1183-4a5e-b745-fe87aa56933e)


Switch back to Client-1 and the Command line will start to ping DC-1 successfully. 

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/7d32911a-e2da-4573-ba3a-1ace7672db8a)


<h3>Step 3: Install Active Directory</h3>

The third step is to install Active Directory Domain Services. Go back into DC-1 Open the Server Manager in DC-1. This should normally open whenever DC-1 is logged into for the first. It can also be found in the Start menu.

In Server Manager, click "Add roles and features." Follow the prompts. At "Server Roles", check "Active Directory Domain Services." Then click "Add Features." Then continue to follow all the installation prompts until it is done. 

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/496431aa-efc6-4131-8e2f-46d148976e89)

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/ee855433-823a-45c4-82dc-1f39ac24ae05)


Once the installation is done, back at the Server Manager Dashboard, click the flag with the yellow hazard sign underneath. Then click "Promote this server to a domain controller."

Click "Add a new forest" and type in the root domain. In this example, it was mydomain.com.

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/62c06a50-bfd3-4629-9625-d9b28508d489)


Follow all the prompts to finish the installation. DC-1 will restart to add all the updates. It may take some time to update and log back in. 


<h3>Step 4: Create an administrator and regular account in Active Directory</h3>
The fourth step is to create an administrator account and a regular account in Active Directory. 

Once DC-1 has restarted, open up Server Manager, click "Tools" in the upper right corner, and select "Active Directory Users and Computers."

In Active Directory Users and Computers, right click the domain (mydomain.com), go to "New" and "Organizational Unit." Create two organizational units for administrators (_ ADMINS) and employees (_ EMPLOYEES). Once done, right click mydomain.com and click referesh to sort the new organizational units to the top. 

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/a2e6c2a8-948e-43b4-b40a-f0d2fe4436bf)
![image](https://github.com/mathew-perez/configure-ad/assets/144407220/c9738198-fcaa-4512-8155-e143cfd6f9b5)


Click on the _ ADMINS organizational unit and right click into the right window pane. Go to "New" and click "User." Fill in the boxes to create a new user. In this example, "Jane Doe" was used and the login name is "jane_admin."

Once the account has been created, right click it and go to "Properties." From there, click the "Member Of" tab, click the "Add..." button, type in domain and click the "Check Names" button. From there, click "Domain Admins" and press "OK" and "Apply" to exit out. 

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/bf518df4-c02f-4ea7-859b-8bd1dd6b546e)

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/8ceba161-0c60-4635-b0e5-31ef1a979f6e)


Log out of DC-1 as "labuser" and log back in as the administrator account that was created (jane_admin). 

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/9a1b7737-37ce-4286-8516-49ab421754e6)

<h3>Step 5: Join Client to the Domain Controller </h3>
The fifth step is to join Client-1 to DC-1. 

<br>

To do this, go back to the Azure Portal. Go to the Client-1 virtual machine and click on the "Networking" section in underneath "Settings" on the left hand side. From there, click on "DNS Servers." Then click, "Custom." Type in DC-1's private IP address (10.0.0.4). 

![image](https://github.com/mathew-perez/configure-ad/assets/144407220/da7f2e57-3f40-4b04-8178-64fb50e513f9)


Restart Client-1 and log back in. Once logged in, right click the Start menu and click on "System."

<p>
<img src="https://i.imgur.com/xkU5A1U.png" height="80%" width="80%" alt="37"/>
</p>

On the right hand side, click "Rename this PC (advanced)." Then click the "Change..." button and type in the domain name (mydomain.com). 

<p>
<img src="https://i.imgur.com/Icy1PH1.png" height="80%" width="80%" alt="38"/>
</p>

Open up the Command line and type the command "ipconfig /displaydns." This will show all the Fully Qualified Domain Names associated to Client-1. It will show that the DNS Servers are associated to DC-1's private IP address. 

<p>
<img src="https://i.imgur.com/ozaaPMB.png" height="80%" width="80%" alt="39"/>
</p>

<h3>Step 6: Setup Remote Desktop for non-administrative users to Client</h3>
Sixth step is to setup Remote Desktop for non-administrative users to Client-1. To do this, log into Client-1. This time, log in using the domain name and the admin account (mydomain.com\jane_admin).

<p>
<img src="https://i.imgur.com/33LPGMy.png" height="80%" width="80%" alt="40"/>
</p>

Once logged in, right click the Start menu and click "System." Then click "Remote desktop" on the right side. From there, click "Add..." and type in "Domain Users." Click "Check Names" and click "OK" to exit out. 

<p>
<img src="https://i.imgur.com/f2nqqY3.png" height="80%" width="80%" alt="41"/>
</p>

<p>
<img src="https://i.imgur.com/GNZgZQw.png" height="80%" width="80%" alt="42"/>
</p>

<p>
<img src="https://i.imgur.com/250dtjg.png" height="80%" width="80%" alt="43"/>
</p>

<h3>Step 7: Create users in Active Directory using Powershell script</h3>
Seventh and final step is to use Powershell to create users. 

<p></p>

The script was created by [Josh Madakor](https://github.com/joshmadakor1). Click [here](https://github.com/joshmadakor1/AD_PS/blob/master/1_CREATE_USERS.ps1) for the source code.


The script below creates 10,000 users with the password "Password1."

<p></p>


```
# ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password1"
$USER_FIRST_LAST_LIST = Get-Content .\names.txt
# ------------------------------------------------------ #

$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force
New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false

foreach ($n in $USER_FIRST_LAST_LIST) {
    $first = $n.Split(" ")[0].ToLower()
    $last = $n.Split(" ")[1].ToLower()
    $username = "$($first.Substring(0,1))$($last)".ToLower()
    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $first `
               -Surname $last `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_USERS,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
}
```

Go back into DC-1 and open Windows Powershell from the start menu. Right click it and "Run as administrator."

<p>
<img src="https://i.imgur.com/GM8VLuY.png" height="80%" width="80%" alt="44"/>
</p>

<p>
<img src="https://i.imgur.com/oLTbGLc.png" height="80%" width="80%" alt="45"/>
</p>

Copy and paste the script into a new Powershell console. I modified the script to only create 10 users so it is easier to manage and play around with. 

<p>
<img src="https://i.imgur.com/2Yot0nU.png" height="80%" width="80%" alt="46"/>
</p>

Once the users have been created, go back to Active Directory Users and Computers. All the users that were created were placed into the _ EMPLOYEES organizational unit. Choose one user (fihapi.nile) and log into Client-1 with the user. Remember the password is Password1. 

<p>
<img src="https://i.imgur.com/QzRho2A.png" height="80%" width="80%" alt="47"/>
</p>

<p>
<img src="https://i.imgur.com/DgfxJtj.png" height="80%" width="80%" alt="48"/>
</p>

<h3>Bonus Step: How to unlock users' accounts and reset passwords</h3>
In order to unlock a user's account, right click the user account and click "Properties." 
Click on "Unlock Account." You can also right click the user account and "Reset Password..."

<p>
<img src="https://i.imgur.com/HTcYBBU.png" height="80%" width="80%" alt="49"/>
</p>

<p>
<img src="https://i.imgur.com/lNfDusu.png" height="80%" width="80%" alt="50"/>
</p>

<p>
<img src="https://i.imgur.com/HrMlyi7.png" height="80%" width="80%" alt="51"/>
</p>

Thank you for checking out my Active Directory tutorial! I hope you were able to learn and build some intuition on how to use Active Directory. I would suggest doing this exercise several times in order to build the knowledge and skills in Active Directory. Especially if you are trying to shoot for an IT job, where Active Directory is used heavily. 

<p></p>

**REMEMBER TO DELETE YOUR RESOURCES ONCE YOU ARE DONE WITH THE LAB!**
