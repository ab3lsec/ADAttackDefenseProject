# An Overview on Active Directory
- Active Directory is used in Windows OS.
- They are like a phonebook which is used to keep a record of information about objects like Usernames, passwords, their Login Names etc..
- Active Directory uses "Kerberos" for authentication
- Non-Windows OS like Linux can also authenticate to Active directory using LDAP or RADIUS.

### WHY AD IS USED?
Just like a phone book organizes names, phone numbers, and addresses, the Active Directory organizes information about each person and thing on the network. It stores details like their names, passwords, what they can do on the network, and where they are located.
For example, if a computer is part of a AD Environment, then its user credentials are stored centrally in the Active Directory database. This centralization allows users to log in to any computer within the same Active Directory domain using their credentials.

### SOME BENEFITS OF ACTIVE DIRECTORY:
- Hierarchical organisational structure
- A single point of access to network resources.
- It provides a centralized location for managing user and computer accounts, which can save time and increase efficiency for IT administrators.
- It provides a range of security features, including password policies, group policies, and access controls, which can help to protect the network from unauthorized access
- Designed to support large networks with many users and devices, and can easily scale to meet the needs of growing organizations.

### WHY ATTACKING AD IS IMPORTANT?
- Because 90% of the top companies uses AD as its Identity Management Service.
- One of advantage of attacking AD is we don't need any prewritten known exploit, we can just abuse its features, trust, components etc...

## PHYSICAL AD COMPONENTS:

#### DOMAIN CONTROLLERS:
- These are like the brains of the Active Directory system.
- It hosts the copy of  AD Directory Store (AD DS) which contains all the information about each objects.
- It also provides Authentication and Authorization. 
- It has ADMIN access to all resources like User Accounts/ Network Services
- A domain controller can be used to add new Users, Groups and Policies

If we somehow manage to compromise DOMAIN CONTROLLER, then we can compromise the entire internal network. Because we can make changes to all the systems in the domain just by using the Domain Controller. 
There Domain controller is a major target while attacking AD

#### ACTIVE DIRECTORY DOMAIN SERVICES (AD DS) STORE :
- The AD DS contains the ***"ntds.dit"*** file (very sensitive)
- ***"ntds.dit"*** file is stored by default  in ``` %SYSTEM ROOT%\NTDS FOLDER```
- Any Windows server that has AD DS server role installed is called a Domain Controller (DC). Organizations can have multiple DCs, which are at the center of Active Directory and control the rest of the domain.

This is a very sensitive file because it contains everything stored in AD with password hashes of all accounts and User. These hashes can be later used to LOGIN via Pass the Hash or Golden ticket attacks.

## LOGICAL AD COMPONENTS:

- **DOMAINS**: Domains are used to group and manage objects within an Active Directory Environment. Domains consists of various Organisational units like Users, Groups, Computers etc..

- **TREES**: A Domain Tree can be defined as a combination of multiple domains. Each domain in a tree shares a two-way transitive trust relationship with other domains.

- **FORESTS**: A domain Forest is a collection of one or more trees. Objects in different forests are not able to interact with each other unless the administrators of each forest create a trust between them.

- **ORGANIZATIONAL UNITS (OUs)**: OUs are containers used to organize and manage objects within the AD. There are different objects in AD like Users, groups, computers etc.. Therefore each objects are in separate organisational units. OUs allow admins to logically group related objects together based on various criteria


### USERS IN AN ACTIVE DIRECTORY NETWORK:

The four main types of users you’ll find in an Active Directory network include:

- **Domain Admins**: control the domains and are the only ones with access to the DC.
- **Service Accounts**: these are for the most part never used except for service maintenance.
- **Local Administrators**: can make changes to local machines as an administrator and may even be able to control other normal users, but they cannot access the domain controller
- **Domain Users**: these are other normal users present under the domain.
