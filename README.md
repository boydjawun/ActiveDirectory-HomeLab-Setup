# Active Directory Home Lab Setup(System Administration)
This diagram shows an overview of the setup of the Home Lab including:
- The Domain Controller with two adapters, one for the outside internet and one for a private network for the clients to connect to. The Domain Controller will also have RAS/NAT set up for clients to access the internet through the Domain Controller. Lastly DHCP is set up to provide IP Addressing to clients accessing the internet
- Windows 11 client, with an adapter connected to the Domain Controller's private network
![image.png](images/image.png)

# Description
This GitHub repository demonstrates my expertise in system architecture, domain management, and building/managing complex network environments through a hands-on Active Directory (AD) home lab setup using VMware virtualization software. The project involves configuring Microsoft Windows Server 2022 to host Active Directory Domain Services (AD DS) and deploying a Domain Controller (DC). A PowerShell script is utilized to automate the creation and addition of 1,000 users to AD, highlighting efficient administrative task automation.
Following DC deployment, a Windows 11 Pro virtual machine is created as a client, configured with appropriate network settings, and joined to the domain. The setup culminates in logging into the client VM using one of the script-generated domain user accounts, simulating a basic corporate network environment.This project serves as a practical showcase for networking, virtualization, and AD administration skills.

# Objectives
- Download and install VMWare
- Download Windows 11 ISO and Windows Server 2022 ISO, to install the two operating systems on two seprate virtual machines

### Create the First Virtual Machine
- The domain controller, which is going to host Active Directory
- Two network adapters
    - one connects to the outside internet
    - The other used to connect to the virtual machine’s private network that the clients are going to connect to
- Install Server 2022 and assign IP addressing for the internal network, name the server, install Active Directory and create the Domain
- Configure NAT and routing so the clients on the private network can reach the internet through the domain controller
- Setup DHCP on Domain Controller so when Windows 11 Virtual machine is created, it can automatially get an IP address
- Run Powershell script to create 1000+ users in the in the Active Directory automatically

### Create the Second Virtual Machine

- Install Windows 11
- Connect Windows 11 Virtual Machine to private Domain Controller’s network
- Name the machine “Client 1” and join it to the Domain
- Then log into it w/ one of the created domain accounts

# Prerequisites + Tools

- Install VMWare([https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion))
- Install Windows 11 ISO([https://www.microsoft.com/en-us/software-download/windows11](https://www.microsoft.com/en-us/software-download/windows11))
- Install Windows Server 2022([https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022))
- Powershell
- Active Directory
- CMD

# Virtual Machine 1(Windows Server 2022)

- Open VMware application and click “Create a New Virtual Machine”

![image.png](images/7f889888-e884-49e6-83a6-75029c0a634a.png)

- Select the recommended configuration and click Next >
- Select the Windows Server 2022 ISO file and click Next >

![image.png](images/image%201.png)

- Did not use a Windows Product Key becasue this is just a demonstrative lab project
- Click Next > as password is optional and click yes at the Window Product Key pop up
- Change the Virtual Machine’s name to “DC”

![image.png](images/image%202.png)

- Leave the Disk Capacity as it is, 60.0 GB and click Next >, then click finish
- My Virtual Machine only has one adapter, two is needed for the outside internet connetion and private internet connection for the clients
- To add another adapter, click “Edit virtual machine settings”

![image.png](images/image%203.png)

- Click “Add…”, then select Network Adapter and click Finish
- Select NAT for the first Network Adapter and VMnet0 for the second Network Adapter, **then click Ok
- I also removed the Floppy disk drive

![image.png](images/882dbc30-12eb-4b09-b97f-665bafd12b8f.png)

![image.png](images/image%204.png)

- Click “Power on this virtual machine”
- I used Windows Server 2022 Standard Evaluation (Desktop Experience)
- Select “Custom: Install Microsoft Server Operating System only (advanced)"
    - Click “New” , then “Apply”, then click Ok
    - After partitioning the drive, click Next

![image.png](images/image%205.png)

![image.png](images/image%206.png)

- After partitioning the drive, allow the machine to restart and set Administrator Password
- Log in with Administrator password
- Install VMware tools VM > Install VMware tools
    - Click Run setup.exe
    - Select Complete setup type then Next >, then Install

### Locating NAT Adapter

- Select “Change adapter options”

![image.png](images/29aa130c-9fd8-420a-8c6c-0cdebf59cb4b.png)

- I have to figure out which NIC serves as NAT and name them appropriately
    - Right clicking an adapter then clicking “Status”, the click “Details”. Reveals IP Addressing information
    - Ethernet0 is the NAT adapter because it’s DNS is assigned to “localdomain”

![image.png](images/image%207.png)

### Renaming PC

- Right click the Windows icon on the bottom left of the screen, click System
- Click “Rename this PC” and add a name for the PC. I chose DC for Domain Controller

![image.png](images/image%208.png)

### Give IP Address to X_internal_X Adapter

- View the properties of the X_internal_X adapter, then click “Internal Protocol Version 4 (TCP/IPv4)"

![image.png](images/image%209.png)

- Select “Use the following IP address”. I used the IP address from the diagram(172.16.0.1). Also I omit the “Default Gateway” because the Domain Controller serves as the gateway. Used a loopback address(127.0.0.1) to use the Domain Controller as the DNS. Then click OK

![image.png](images/image%2010.png)

### Install Active Directory Domain Service

- In the Server Manager click “Add Roles and Features”

![image.png](images/image%2011.png)

- Select “Role-based or feature-based installation” for Installation Type and select the recently created Server (DC) in the Server Selection section

![image.png](images/image%2012.png)

- For the Server Roles section, select “Active Directory Domain Services” then click Add Features

![image.png](images/image%2013.png)

- Click Next > until you get to the Confirmation section and click “Install”. Be patient as it takes some time to install, then click Close when the installation process is finished

![image.png](images/image%2014.png)

- After completing the installation process, the Active Directory Domain Services software is now downloaded, now the Domain needs to be created. On the Server Manager click the flag at the top with the yellow triangle

![image.png](images/image%2015.png)

- Click “Promote this server to a domain”

![image.png](images/image%2016.png)

- Select “Add a new forest”. Specify the root domain name for the Domain. I used “mydomain.local”

![image.png](images/image%2017.png)

- Type the Password for your Domain Controller in the Domain Controller Options section then click Next >

![image.png](images/image%2018.png)

- In the Additional Options section, wait for the NetBIOS name to generate for the Domain and click Next > until getting to the Prerequisites Check section and wait until the check is passed and click Install. After the installation, my computer Virtual Machine restarted and I logged back in with my Administrator account

![image.png](images/image%2019.png)

### Create our my Domain Admin Account

- I am going to create my own Domain Admin account instead of using the builtin Administrator account
- Click Start > Windows Administrative Tools > Active Directory Users and Computers

![image.png](images/image%2020.png)

- Create and Organizational Unit for the Admin account that is being created. Think of the Organizational Unit as a folder used for our Admin account
- Click right mydomain.local > New > Organizational Unit

![image.png](images/image%2021.png)

- Give a name to the Organizational Unit

![image.png](images/image%2022.png)

- Once the Organizational Unit is created, it will be added to the list of folders under mydomain.local. Now create a new user in the Organizational Unit
- Right click mydomain.local > New > User

![image.png](images/image%2023.png)

- Fill in the info for the new user including first name, last name, and User logon name. I used a-jboyd to signify that this is an admin account, then click Next > and enter the password for the user

![image.png](images/image%2024.png)

- I selected the “Password Never Expires” box to refrain from dealing with the password during this Lab then click Next >, then Finish

![image.png](images/image%2025.png)

- Although the user is created and has an a- in the username it is still not an admin yet. To make it a domain admin, right click the created user > Properties > Member Of. Click “Add…” and type “Domain Admins”

![image.png](images/image%2026.png)

- Click the “Check Names” button then click OK
- Then click Apply on the “Members Of” menu, then click OK

![image.png](images/1d638acf-2f45-4ec9-86a8-04b2ee60b214.png)

- Now I have my own Domain Admin account
- After creating the Domain Account I signed out of the Builtin Administrator account and signed in using the created Domain Admin Account

![image.png](images/image%2027.png)

### Create RAS(Remote Access Services)/NAT(Network Address Translation)

- The creation of RAS/NAT allows our Windows 11 client to be on a private virtual network, but still be able to access the internet through the Domain Controller
- In there Server Manager, click “Add roles and features”. Select “Role-based or feature-based installation” for installation type
- Select the Server in the Server Selection section and click Next >

![image.png](images/image%2028.png)

- Select “Remote Access” in the Server Roles section then click Next >

![image.png](images/image%2029.png)

- Click Next > until the “Role Services” section. Select “Routing” from the list of services, then click Add Freatures

![image.png](images/image%2030.png)

- I clicked Next > until it got to the Confirmation section and clicked Install. Wait on the installation, then click Close
- On the Server Manager click Tools> Routing and Remote Access

![image.png](images/image%2031.png)

- Right click the Domain > Configure and Enable Routing and Remote Access

![image.png](images/image%2032.png)

- Click Next > once the Setup Wizard pops up
- Select “Network address translation(NAT)" to allow clients to the internet. Click Next >

![image.png](images/image%2033.png)

- Select “Use this Public interface to connect to the Internet” and click the “_INTERNET_” interface. This is why it was important to name the Adapters to be able to tell the difference between public and private interfaces

![image.png](images/image%2034.png)

- After clicking Finish, there was a pop up, I pressed OK

![image.png](images/image%2035.png)

- Wait until the Remote Access Service finished initializing

![image.png](images/image%2036.png)

- Now I have my Domain, set up along with RAS and NAT

![image.png](images/image%2037.png)

### Setting up DHCP Server on Domain Contoller

Allows Windows 11 Clients to automatically get an IP address that will let them access the internet even though they are on this private internal network. I will create a scope to give IP Addresses in a defined range.

- In Server Manager click Add roles and features
- Select “Role-based or feature-based installation” for installation type, select the correct Domain in the Server Selection section
- In the Server Roles section, select DHCP Server and AD Features

![image.png](images/image%2038.png)

- Click Next > until the Confirmation section, then click Install, wait for the installation, then click Close
- In the Server manager select Tools > DHCP

![image.png](images/image%2039.png)

- Right click IPv4 and click “New Scope…”, click Next > and name the scope. I named the scope after the range I am using for the IP Addresses

![image.png](images/image%2040.png)

- Click Next > and clarify the Start and Stop IP Addresses for the DHCP and give the network a mask of /24, click Next >

![image.png](images/image%2041.png)

- No Exclusions were added, click Next >. Set the duration for how long a client can have their assigned IP address before it needs to be refreshed. I set it to 8 days. So after 8 days, of the client having an IP Address, it will need to be refreshed. Click Next >

![image.png](images/image%2042.png)

- Select “Yes, I want to configure these options now” to configure DHCP options for the scope. This allows me to tell the Clients which server to use for the Gateway Router. In this demonstration, the clients will use the internal NIC on the Domain Controller as their default Gateway/ Router. I enter the Domain Controller IP Address and click “Add” then Next >

![image.png](images/image%2043.png)

- I chose my domain the I created (mydomain.local) as my parent domain and clicked Next > until I was asked about Activating the scope and I selected “Yes, I want to activate this scope now”, then Next >, then Finish

![image.png](images/image%2044.png)

- Now I have to authorize the server and refresh. Right click the DC > Authorize, then right click the DC again, and click refresh

![image.png](images/image%2045.png)

![image.png](images/image%2046.png)

- Now the scope I created is under the IPv4 section

![image.png](images/image%2047.png)

- There are no Address Leases yet because I haven’t created the client computer yet

![image.png](images/image%2048.png)

### Use PowerShell Script to create Users in Active Directory

- First, I have to make a configuration that lets me browse the internet from the Domain Controller. This is usually not done in a production environment, this is only done for lab purposes
- In the Server Manager, click “Configure this local server”

![image.png](images/image%2049.png)

- Click “On” at “IE Enhanced Security Configuration”. Then select off for both Administrators and Users. This disables the security features on the Domain Controller to be able to retrieve my PowerShell script from the internet

![image.png](images/image%2050.png)

- Next, download the PowerShell script with this link on the Domain Controller Virtual Machine by pasting this link in the Domain Controller web browser: [https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) and downloading the zip file, then extract the files. I included the folder(AD_PS-master) equipped with the Powershell script and list of names in the names.txt file above

![image.png](images/image%2051.png)

- Click the names.txt file in the folder and I added my name at the top of the names.txt file name list and saved it

![image.png](images/image%2052.png)

- Click Start > Windows PowerShell > Windows PowerShell ISE and select to run it as administrator

![image.png](images/image%2053.png)

- Inside Windows PowerShell ISE, go to the folder icon in the top taskbar

![image.png](images/image%2054.png)

- Go to the location of the downloaded PowerShell script and select 1_CREATE_USERS

![image.png](images/image%2055.png)

![image.png](images/image%2056.png)

- Before running this PowerShell script, I have to enable the execution of all scripts. Do this by typing: Set-ExecutionPolicy Unrestricted and press enter and select “Yess to All” for the Execution Policy Change pop up . This is for lab purposes only as this is a security feature for the Domain Controller

![image.png](images/image%2057.png)

- To run, path has to be defined to locate the PowerShell script. Change Directory into the script file
- After locating the file, click enter to run it and click “Run Once” at the Security Warning

![image.png](images/image%2058.png)

- After the script has finished, I look on “Active Directory Users and Computers” and there are alot of users that was created from that PowerShell script that I ran

![image.png](images/image%2059.png)

- The script ran fine, but there were a couple of errors because of duplicate names that were on the list. This did not effect the script completing
- Also, right clicking _USERS > find.. I can search for users

![image.png](images/image%2060.png)

# Virtual Machine 2(Windows 11)

Creating the second Virtual Machine for Windows 11

- Navigate to the home tab on VMWare and click “Create a New Virtual Machine”

![image.png](images/image%2061.png)

- Select the recommended configuration type, then select the Window 11 ISO file

![image.png](images/image%2062.png)

- Name the Virtual Machine: “CLIENT 1”

![image.png](images/image%2063.png)

- Enter the password for the Virtual Machine, and the disk size(I left the size at the default size), then click finish
- Click “Edit virtual machine settings”
- Click “Network Adapter”, then select Custom. This disables NAT and restrict internet access in my local network. Windows 11  client can access the internet but only through getting an IP address from the Domain Controller. For this to happen, I configured this Virtual Machine on the same network as the Domain Controller Virtual Machine

![image.png](images/image%2064.png)

- This picture of the internal network adapter from Windows Server 2022 set to VMnet0 corroborates that, the Windows 11 Virtual Machine is on the same network

![image.png](images/image%2065.png)

- Select Windows 11 Pro

![image.png](images/image%2066.png)

- A page popped up to name my device and I clicked “Skip for now”
- I also clicked to set up my device for Personal Use
- If the “How would you like to set up this device?” screen comes up, choose “Set up for work or school”

![image.png](images/image%2067.png)

- Select “Sign in options” right below the email address bar

![image.png](images/image%2068.png)

- Click “Domain join instead”

![image.png](images/image%2069.png)

- For the “Who’s going to use this device” page, enter “user”, create a password for the user, and choose the security questions for the user

![image.png](images/image%2070.png)

- I turned off all of the privacy settings for the the user account. This is done for lab testing purposes
- After setup, I ran ipconfig in the terminal to see that the IP Addressing the I set up with DHCP works and I am able to access the internet through the Domain Controller

![image.png](images/image%2071.png)

- I can also ping my Domain Controller which shoes that there is indeed network connectivity between the Windows 11 and the Windows Server 2022 Virtual Machines

![image.png](images/image%2072.png)

- I change the hostname of the Windows 11 system by right clicking the start menu > System. Then click rename

![image.png](images/image%2073.png)

- After renaming, I restarted the system

### Joining Windows 11 to Windows Server 2022 (Domain Controller)

- Click on the Window’s Icon and search “domain” and click on “Access work or school” and click the “Connect” button

![image.png](images/image%2074.png)

- Click “Join this device to a local Active Directory domain”

![image.png](images/image%2075.png)

- Type in the name of the domain and click Next

![image.png](images/image%2076.png)

- I used my Administrator Account that I previously made as credentials to join to the domain, and for account type I chose Administrator, then I restarted my system and logged back in as user a-jboyd

![image.png](images/image%2077.png)

- Since I have Windows 11 Virtual Machine joined to the Domain Controller, I am able to view it in the Address Leases folder on the Domain Controller
- In the Server Manager click Tools > DHCP > dc.mydomain.local > IPv4 > Scope > Address Leases

![image.png](images/image%2078.png)

- These leases are created after the DHCP server gives the client an IP Address
- The client computer is also visible in the computers folder under “Active Directory Users and Computers”
- In the Server Manager click Tools > Active Directory Users and Computers > mydomain.local > Computers

![image.png](images/image%2079.png)

### Logging in as another user account

I am now logging in as another user account established within mydomain.local from running the PowerShell to check for proper configuration 

- Check the list of users on the Domain Controller and chose one to use
- I restarted Windows 11 to get back to the login screen. Click “Other user” for the sign in and enter the user’s credentials

![image.png](images/image%2080.png)

- I change the user’s password to log in so that I will know the password for the acastleberry
- In the Server Manager, go to the list of users find one to use, and right click the user. Click “Reset Password”, change the password, then click OK

![image.png](images/image%2081.png)

![image.png](images/image%2082.png)

- Go back to the Windows 11 Virtual Machine and log on

![image.png](images/image%2083.png)

- Running Whoami in the terminal, I can see that I am the user acastleberry and this user is a member of mydomain

![image.png](images/image%2084.png)
# Organizational Units and Group Policies

# Organizational Units

>Organizational Units are used to define sets of users with similar policing requirements

In this scenerio I have a tech company, MyDomain, inc.  and different departments need to have different rules and restrictions. This will be done using Organizational Units, container objects that let you classify users and machines

# Creating Organizational Units

Now I create each individual Organizational Unit for the Management, Marketing, Sales, and IT departments for MyDomain, inc

- In the server manager on the Windows Server 2022 Virtual Machine click tools > Active Directory Users and Computers
- Right click mydomain.local > New > Organizational Unit

![image.png](attachment:a5ce0c64-4d5d-41e8-ab7d-c6d67886ff91:image.png)

- Name the Organizational Unit

![image.png](attachment:09a78727-16c5-4ac8-bee2-128343751945:image.png)

- I see that the Organizational Unit is added to mydomain.local

![image.png](attachment:7a19fae3-cc27-44f3-af9c-5eb17c206997:image.png)

- Now create the other Organizational units for IT, Sales, and Marketing

![image.png](attachment:b918a18e-acdd-4bd1-895b-f235f1533dfd:image.png)

### Adding Users to Each Organizational Group

I am adding users to each organizational group and delegating privileges

- Click _USERS, select a user to move. Right click the User and select “Move…”

![image.png](attachment:746eea79-910b-4637-aaa7-d634ba0a9560:image.png)

- Click the Management Organizational Unit and click Ok. Here, I put user acastleberry in the Management Organizational Unit. Next, I fill the other organizational units with 3-4 users for each department

![image.png](attachment:1daed8c7-d767-4353-969d-30fc2e2bb093:image.png)

# Delegate Control over OU

**>Delegation** allows you to grant users specific privileges to perform advanced tasks on OUs without needing a Domain Administrator

Now that I have the Organizatonal Units created, I want to delegate the control of resetting user passwords for every department to users in the IT Department

- Right click the “Management” Organizational Unit > Delegate Control

![image.png](attachment:a946e843-ab31-46ff-9515-3dfaf2546d6d:image.png)

- Click Next > when the Delegation of Control Wizard pops up, then click add to add the users of the IT Organizational Units

![image.png](attachment:aeb75364-ad8a-46c7-aaea-1d3c48c67767:image.png)

- I added a user’s name and clicked “Check Names” to verify the user, then click space and typed the other names with the same procedure and click Ok

![image.png](attachment:f1fc2260-b134-4b9d-a697-08929a032284:image.png)

- The list of users are shown that have been selected from the IT department. Click Next >

![image.png](attachment:5bcd1401-edd6-49ec-a15b-680412babe91:image.png)

- Select “Reset user passwords and force password change at next logon”

![image.png](attachment:a28ba970-f921-4d38-995d-be7226d0ca6f:image.png)

- Click Next > then click Finish

![image.png](attachment:f7695d9c-1293-41c1-8b62-5c896fe862b5:image.png)

### Verifying the Delegation

I log in as cdally, from the IT Organizational Group, on CLIENT 1 and reset the password for Beverley(btagg) from the Marketing Group. To be able to interact with the Active Directory from CLIENT1, I had to download RSAT:Active Directory Domain Services and Lightweight Directory Service Tools

>I had to be logged in as a local administrator for this computer to download RSAT. I logged in with my administrator account, downloaded it, and logged back in as cdally

- Settings > Apps > “optional features” in the search bar

![image.png](attachment:21d8272d-eb61-416f-a5e6-86ebdb863a37:image.png)

- Click “View Features” > “See available features”
- Type RSAT in the search bar, look for RSAT: Active Directory Domain Services and Lightweight Directory Services Tools

![image.png](attachment:d79a58f1-d300-4469-b88a-f526a68eca69:image.png)

- After downloading, I can type Active Directory in the search bar and it appears on my CLIENT1 machine

![image.png](attachment:0ff07553-09df-43a2-a7f1-bae18fb0c367:image.png)

- Next, I open PowerShell and enter a command to force a new password reset for Beverley(btagg) at her next logon

>Make sure that the account used has Password never expires unchecked in the user account properties in Active Directory. Tools > Active Directory Users and Computers > Double Click User > Account Tab

- The next time Beverley logs in, she will be forced to reset her password

![image.png](attachment:c9ffffbf-9806-4a7f-bab6-078a7704672c:image.png)

- I try to reset the password of a user that is not in an Organizational Unit that Carmelo(cdally) has delegation of and failed due to access denial, which shows the delegation process is working fine

![image.png](attachment:f5e96707-6231-497e-9c0e-60c5faf68d20:image.png)

- Carmelo also does not have access to move users to other Organizational Groups. Search: Active Directory Users and Computers > Click an OU > Right-Click a User > Move…
- As seen, Carmelo cannot move user btagg becasue he only has delegated permissions to change and reset her password

![image.png](attachment:3406329b-5a07-4f79-bc00-b9c6839131fb:image.png)

- I log out of Carmello’s account and to access Beverley’s I am prompted to enter a new password to enter, further verifying that the delegation for cdally works fine

![image.png](attachment:78057cc9-6bae-42d2-b47f-74f945e1eb22:image.png)

# Group Policy Objects

Group Policy Objects(GPOs) are how Windows manages policies for different Organizational Units. Here, I’m going to create a Group Policy Object that blocks all non-IT Organizational Unit members from accessing the control panel on their machines

### Creating the Group Policy

- Go to Windows Server 2022 Virtual Machine > Search “Group Policy Management”

![image.png](attachment:7aee7699-637f-42de-b0a6-75e786a6405e:image.png)

- Navigate to the “Group Policy objects” folder in mydomain.local and Right-Click > New > Enter a name for the Group Policy. I chose “Restrict Control Panel Access” and click OK

![image.png](attachment:704a4098-c787-42f6-98d6-fc655dd01a9f:image.png)

### Editing the Group Policy

- Right Click the GPO that was just created > Edit > User Configuration > Policies > Administrative Templates > Control Panel

![image.png](attachment:31cc762e-31d7-4c0c-9af1-355b1e342b62:image.png)

- Double-click “Prohibit access to Control Panel and PC settings” to enable it

![image.png](attachment:ae5b28f8-9ac2-42b8-9f58-f5dfb464d084:image.png)

- Click “Apply” then OK. The restriction is shown here as “Enabled”

![image.png](attachment:37792cc1-f207-40f6-aa4b-e54fc71daf5e:image.png)

- Exit from the Group Policy Management Editor and go back to the Group Policy Objects folder. Copy the “Restrict Control Panel Access” GPO to the Marketing, Sales, and Management Organizational Units to enforce the rules to the group members

![image.png](attachment:56c6950d-db80-4ffc-83d0-1b8cd8fe3a16:image.png)

### Verifying Group Policy Object

Now I will log in as Beverley(btagg), a member of the Marketing Organizational Unit, and try to access the control panel on CLIENT1

![image.png](attachment:5ebfbb4c-343a-4d50-b27d-d5ce14263f03:image.png)

- I received a restriction pop up that prevents Beverley from accessing the control panel due to the Group Policy Control Panel restrictions
