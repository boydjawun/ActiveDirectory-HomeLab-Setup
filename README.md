# Active Directory Home Lab Setup(System Administration)
This diagram shows an overview of the setup of the Home Lab including:
- The Domain Controller with two adapters, one for the outside internet and one for a private network for the clients to log on to. The Domain Controller will also have RAS/NAT set up for clients to access the internet through the Domain Controller. Lastly DHCP is set up to provide IP Addressing to clients accessing the internet
- Windows 11 client, with an adapter connected to the Domain Controller's private network
![image.png](images/image.png)

# Description
This project showcases knowlege of system architecture, domain management, and proficiency in constructing and managing complex network environments by configuring Microsoft Server 2022 to host Active Directory and deploying a Domain Controller. Also using a PowerShell script to create add users to the Active Directory demonstrates the ability to streamline administrative tasks efficiently. After deploying the Domain Controller, I create the Windows 11 Pro Virtual Machine and join it to the Domain Controller. The client is then joined to the Domain Controller and logged into using one of the created users from the PowerShell script

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

- Next, download the PowerShell script with this link on the Domain Controller Virtual Machine by pasting this link in the Domain Controller web browser: [https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) and downloading the zip file, then extract the files

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
