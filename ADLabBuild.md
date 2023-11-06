# Active Directory Attack/Defense Lab Build

For security researchers, it’s very important to know how AD works and it’s weaknesses. For this Active Directory project, I embarked on building an Active Directory Lab Environment in a Virtualized setup. 
Utilizing online resources and guided tutorials, especially one from TheCyberMentor on YouTube, I successfully constructed a secure and functional lab setup. For me, This lab served as a testing ground for exploring the How AD works, What are its weakness and vulnerabilities and corresponding security measures for Defending AD.

In this document, I outline all the steps I followed to build the Active Directory lab environment.

##### LAB REQUIREMENTS:

For Building this lab, I have used the following software and Operating systems.
- VMWare Workstation 17 player
- One [Windows Server 2019](https://www.microsoft.com/en-in/evalcenter/evaluate-windows-server-2019) (For Domain Controller)
- One [Windows 10 Enterprise](https://www.microsoft.com/en-in/evalcenter/evaluate-windows-10-enterprise) (For User Machine 1)
- One [Windows 10 Enterprise](https://www.microsoft.com/en-in/evalcenter/evaluate-windows-10-enterprise) (For User Machine 2)
- One [Kali Linux](https://www.kali.org/get-kali/#kali-virtual-machines) (For Attacker)

For System Requirements, the ideal amount of RAM is 16 GB. So that you can allocate enough RAM for each Operating System. I have used 4 GB for my Kali box and 2GB each for all Windows machines on the domain. 

##### INSTALLING WINDOWS SERVER 2019 (DOMAIN CONTROLLER)

1. Select "Create a new Virtual Machine"
2. Now Select the Windows server 2019 ISO image that we downloaded.
3. Select the version "Windows Server 2019 Standard Core"
4. In the final page, Untick "Power on this Virtual machine after creation"
5. Now go to VMWare Settings > Remove Floppy disk
6. Select "NAT" as the Network adapter
7. After setting up everything, Start the VM
8. Press any key to enter the Installer setup and Hit "Install Now"
9. Select 'Windows Sever 2019 Standard Evaluation (Desktop Experience)'
10. Select Custom Install > Select Unallocated Space > New > Apply > Next
11. Set the Password for Administrator: `P@$$w0rd!` > Finish

##### CREATING A DOMAIN & A DOMAIN CONTROLLER:

Here we will create a new domain named `HERO.local` and promote the Windows Server 2019 machine as the Domain Controller for `HERO.local`
 
2. Search > View Your PC Name > Rename this PC > Ex: `JUSTICE-DC` and Restart
3. Now create a new domain by navigating to Server Manager > Dashboard > Manage > Add Roles and Features > Role-based install or feature-based Installation > Under Server Roles, Select "Active Directory Domain Services" > Add features > Next > Install
4. Once its done, we will promote this machine as the Domain Controller
5. Server Manager > Flag icon > 'Promote this server to a domain controller'
6. Select "Add a new forest" > Give Root domain name: `HERO.local` > Next
7. Set DSRM Password (Use the Same Password: `P@$$w0rd!`)  > Next for all > Install

##### INSTALLING WINDOWS 10 ENTERPRISE (USER MACHINES)

1. Select "Create a new Virtual Machine"
2. Now Select the Windows server 2019 ISO image that we downloaded.
3. Select the version "Windows 10 Enterprise"
4. In the final page, Untick "Power on this Virtual machine after creation"
5. Now go to VMWare Settings > Remove Floppy disk
6. Select "NAT" as the Network adapter
7. After setting up everything, Start the VM
8. Press any key to enter the Installer setup and Hit "Install Now"
9.  Select Custom Install > Select Unallocated Space > New > Apply > Next
10. Set the Region > Select Keyboard layout > Skip "Add Second Keyboard"
11. Select 'Domain join instead' > Enter Account Name Ex: `Bruce Wayne` > Password `Password1`
12. Search > View Your PC Name > Rename this PC `BATMAN` and Restart
13. Do the same for the Second User Machine `FLASH` and Give Username: `Barry Allen` and Password: `Password2`

##### SETTING UP AD USERS, GROUPS AND POLICIES

After creating a domain `HERO.local` and a domain controller `JUSTICE-DC` , we need to create new users for the User machines that we set Up. For this project we will create 4 new Accounts:
- A New Domain Admin Account: `Clark Kent` (superman@HERO.local)
- Two Domain User accounts: `Bruce Wayne` (batman@HERO.local) and `Barry Allen` (flash@HERO.local)
- A Service Account with Domain Admin Privileges: SQL Service (SQLService@HERO.local)

1. Login to the Domain Controller `JUSTICE-DC`
2. Now we need to add users to the Domain `HERO.local`
3. Server Manager > Dashboard > Tools > Select "Active Directory Users and Computers"
4. Default users `Administrator` and `Guest` can be seen under the `Users` folder with a bunch of other groups.
5. Right Click on the Root Domain name `HERO.local` > New > Organizational Unit > Groups
6. Move all the accounts except `Administrator` and `Guest` to the newly created Groups folder.
7. We need to create a bunch of new domain users for the User machines.
8. Right Click > New > User > Set Full Name: `Bruce Wayne` >Set User logon name: `batman` > Set Password: `Password1` > Select "Password Never Expires" > Next > Finish
9. Right Click on `Bruce Wayne`> Copy > Set Full Name: `Barry Allen` >Set User logon name: `flash` > Set Password: `Password2` > Select "Password Never Expires" > Next > Finish
10. We also need to create a domain admin account. For this, Right Click on  `Administrator` > Copy > Set Full Name: `Clark Kent` >Set User logon name: `superman` > Set Password: `Password2023@!` > Select "Password Never Expires" > Next > Finish
11. We also need to create a Service Account as a domain admin. For this, Right Click on  `Administrator` > Copy > Set Full Name: `SQL Service` >Set User logon name: `SQLService` > Set Password: `MYpassword123!` > Select "Password Never Expires" > Next > Finish

##### CREATING A FILE SHARE:

We need to create a file share, which will open ports 445 and 139. These ports can be very dangerous which is demonstrated in the upcoming attacks.

1. Server Manager > Dash board > Select "File and Storage Services" > Shares
2. TASKS > New Share > SMB Share - Quick > Next
3. Share name: `hackme` > Proceed with Defaults > Create

##### CREATING SPN FOR THE SERVICE ACCOUNT:

Service Principal Name (SPN) are used for Service Accounts which becomes very useful in attacks like "Kerberoasting". Therefore we need to set the SPN for the `SQLService` account that we created.

1. Search > CMD > Run as administrator
2. Set the SPN using following Command:
```
setspn -a JUSTICE-DC/SQLService.HERO.local:60111 HERO\SQLService
```
3. Once its created we can check the new SPN by using:
```
setspn -T HERO.local -Q */*
```

##### DISABLING WINDOWS DEFENDER:

1. Search > Group Policy Management > Run as administrator
2. Expand Forest > Expand Domains > Right Click on `HERO.local` > Create a GPO > Name: `Disable Windows Defender` > OK
3. In `Disable Windows Defender` GPO, Change the "ENFORCE" status to "YES"
4. Expand Forest > Expand Domains > Select `HERO.local` > Right Click `Disable Windows Defender` > Edit
5. Computer Configuration > Policies > Administrative Template > Windows Components > Select Windows Defender Antivirus > Double click 'Turn off Windows Defender Antivirus' > Select "Enabled" > OK

##### CONNECTING ALL MACHINES TO THE DOMAIN:

At this stage, all our machines are set up, all we need to do is add them to the domain and connect them all together.
The things we need to do are:
- Create a folder and share it to the Public
- On `BATMAN` machine, set `Bruce Wayne` as the Local Admin
- On `FLASH` machine, set both `Bruce Wayne` and `Barry Allen` as Local admins

1. Login to any User Machine
2. Create a New folder under` C: Drive` named `Share`
3. Right click on `Share` > Select properties > Share > Share > Select "Yes, turn on network discovery for all public Networks"
4. Grab the IP Address of Domain Controller `JUSTICE-DC`
5. Search "Network Status" > Change adapter options >Right click on Ethernet0 > Double click on IPv4 > Select "Use following DNS server address" > Paste the Domain Controller IP in there > OK
6. Search "Access Work or School" > Connect > Select 'Join this device to local Active Directory domain' 
7. Enter domain name `HERO.local`
8. Join domain as `Administrator` pass: `P@$$w0rd!`
9. When prompt for add an account > Skip > Restart now
10. Now Do the Same for the second user machine `FLASH`
11. After Restarting Both, In the first machine `BATMAN`, Give `Bruce Wayne` Local Administrator rights.
12. Right Click on Start > Computer Management > Local Users and Groups > Groups > Double click Administrators
13. Click on Add > Enter Bruce Wayne's logon name (batman) >Check Names > Apply > OK
14. Do the same for the second machine `FLASH` but make sure to give both `Bruce Wayne` and `Barry Allen` the Local Administrator rights.

15. Now we can verify whether the computers are joined the Domain, Just by logging into the Domain Controller
16. On Domain controller > Server Manager > Dashboard > Tools > Select "Active Directory Users and Computers" > `HERO.local` > Computers
17. Check whether two User Machines `FLASH` and `BATMAN` are present in it
