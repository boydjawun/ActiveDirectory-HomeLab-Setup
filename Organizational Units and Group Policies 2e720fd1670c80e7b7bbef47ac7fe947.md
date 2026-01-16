# Organizational Units and Group Policies

# Organizational Units

>Organizational Units are used to define sets of users with similar policing requirements

In this scenerio I have a tech company, MyDomain, inc.  and different departments need to have different rules and restrictions. This will be done using Organizational Units, container objects that let you classify users and machines

# Creating Organizational Units

Now I create each individual Organizational Unit for the Management, Marketing, Sales, and IT departments for MyDomain, inc

- In the server manager on the Windows Server 2022 Virtual Machine click tools > Active Directory Users and Computers
- Right click mydomain.local > New > Organizational Unit

![image.png](image.png)

- Name the Organizational Unit

![image.png](image%201.png)

- I see that the Organizational Unit is added to mydomain.local

![image.png](image%202.png)

- Now create the other Organizational units for IT, Sales, and Marketing

![image.png](image%203.png)

### Adding Users to Each Organizational Group

I am adding users to each organizational group and delegating privileges

- Click _USERS, select a user to move. Right click the User and select “Move…”

![image.png](image%204.png)

- Click the Management Organizational Unit and click Ok. Here, I put user acastleberry in the Management Organizational Unit. Next, I fill the other organizational units with 3-4 users for each department

![image.png](image%205.png)

# Delegate Control over OU

**>Delegation** allows you to grant users specific privileges to perform advanced tasks on OUs without needing a Domain Administrator

Now that I have the Organizatonal Units created, I want to delegate the control of resetting user passwords for every department to users in the IT Department

- Right click the “Management” Organizational Unit > Delegate Control

![image.png](image%206.png)

- Click Next > when the Delegation of Control Wizard pops up, then click add to add the users of the IT Organizational Units

![image.png](image%207.png)

- I added a user’s name and clicked “Check Names” to verify the user, then click space and typed the other names with the same procedure and click Ok

![image.png](image%208.png)

- The list of users are shown that have been selected from the IT department. Click Next >

![image.png](image%209.png)

- Select “Reset user passwords and force password change at next logon”

![image.png](image%2010.png)

- Click Next > then click Finish

![image.png](image%2011.png)

### Verifying the Delegation

I log in as cdally, from the IT Organizational Group, on CLIENT 1 and reset the password for Beverley(btagg) from the Marketing Group. To be able to interact with the Active Directory from CLIENT1, I had to download RSAT:Active Directory Domain Services and Lightweight Directory Service Tools

>I had to be logged in as a local administrator for this computer to download RSAT. I logged in with my administrator account, downloaded it, and logged back in as cdally

- Settings > Apps > “optional features” in the search bar

![image.png](image%2012.png)

- Click “View Features” > “See available features”
- Type RSAT in the search bar, look for RSAT: Active Directory Domain Services and Lightweight Directory Services Tools

![image.png](image%2013.png)

- After downloading, I can type Active Directory in the search bar and it appears on my CLIENT1 machine

![image.png](image%2014.png)

- Next, I open PowerShell and enter a command to force a new password reset for Beverley(btagg) at her next logon

>Make sure that the account used has Password never expires unchecked in the user account properties in Active Directory. Tools > Active Directory Users and Computers > Double Click User > Account Tab

- The next time Beverley logs in, she will be forced to reset her password

![image.png](image%2015.png)

- I try to reset the password of a user that is not in an Organizational Unit that Carmelo(cdally) has delegation of and failed due to access denial, which shows the delegation process is working fine

![image.png](image%2016.png)

- Carmelo also does not have access to move users to other Organizational Groups. Search: Active Directory Users and Computers > Click an OU > Right-Click a User > Move…
- As seen, Carmelo cannot move user btagg becasue he only has delegated permissions to change and reset her password

![image.png](image%2017.png)

- I log out of Carmello’s account and to access Beverley’s I am prompted to enter a new password to enter, further verifying that the delegation for cdally works fine

![image.png](image%2018.png)

# Group Policy Objects

Group Policy Objects(GPOs) are how Windows manages policies for different Organizational Units. Here, I’m going to create a Group Policy Object that blocks all non-IT Organizational Unit members from accessing the control panel on their machines

### Creating the Group Policy

- Go to Windows Server 2022 Virtual Machine > Search “Group Policy Management”

![image.png](image%2019.png)

- Navigate to the “Group Policy objects” folder in mydomain.local and Right-Click > New > Enter a name for the Group Policy. I chose “Restrict Control Panel Access” and click OK

![image.png](image%2020.png)

### Editing the Group Policy

- Right Click the GPO that was just created > Edit > User Configuration > Policies > Administrative Templates > Control Panel

![image.png](image%2021.png)

- Double-click “Prohibit access to Control Panel and PC settings” to enable it

![image.png](image%2022.png)

- Click “Apply” then OK. The restriction is shown here as “Enabled”

![image.png](image%2023.png)

- Exit from the Group Policy Management Editor and go back to the Group Policy Objects folder. Copy the “Restrict Control Panel Access” GPO to the Marketing, Sales, and Management Organizational Units to enforce the rules to the group members

![image.png](image%2024.png)

### Verifying Group Policy Object

Now I will log in as Beverley(btagg), a member of the Marketing Organizational Unit, and try to access the control panel on CLIENT1

![image.png](image%2025.png)

- I received a restriction pop up that prevents Beverley from accessing the control panel due to the Group Policy Control Panel restrictions