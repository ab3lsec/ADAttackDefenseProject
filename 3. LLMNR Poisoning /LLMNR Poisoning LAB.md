# LLMNR Poisoning LAB DEMO 

To poison LLMNR we need to use a tool called "Responder"
"Responder" will poison the LLMNR requests and listens for any events to occur.

1. Start responder tool with the following Syntax
```bash
responder -I eth0 -dwv
```
-I : used to denote Interface name
-d : enables answers for DHCP requests
-v : enables verbosity

![responder](https://github.com/ab3lsec/ADAttackDefenseProject/assets/87868050/fceac1aa-f6b2-42bc-b15a-9cbafca0120a)
![resp2](https://github.com/ab3lsec/ADAttackDefenseProject/assets/87868050/4a5274ae-777d-4bf2-a237-579d6c299f7e)


2. Once started, responder will start listening for any event to occur on the network - that means anyone to request a host that cannot be resolved by DNS. So lets go to "BATMAN" and make a fake request to trigger an event.

![fakeserver](https://github.com/ab3lsec/ADAttackDefenseProject/assets/87868050/969894c7-c206-4d35-a064-594e67888e06)


In this case "fakeserver" is not a host and DNS will fail to resolve, Now the victim will look to resolve using LLMNR.
Once the victim uses the LLMNR, the "Responder" tool will respond for request and capture the username and NTLMv2 Hash of the victim.

![hash llmnsr](https://github.com/ab3lsec/ADAttackDefenseProject/assets/87868050/88290900-d3a1-47a3-8b07-a8c06005c39c)


We have successfully obtained the username (`HERO\batman`) and the NTLMv2 hash of the domain user "Bruce Wayne".
We can crack this NTLMv2 hash using `hashcat` with the following syntax:

```
hashcat -m 5600 ntlmhash.txt rockyou.txt --force
```

![crackpass](https://github.com/ab3lsec/ADAttackDefenseProject/assets/87868050/d0df7654-d875-450e-8086-6909babb282d)



## LLMNR POISONING DEFENSE

Now through these lab we understood that turning ON LLMNR feature is a bad security practise. 
So here are some defences to prevent LLMNR poisoning attack:

##### 1. DISABLE LLMNR FEATURE
To disable it navigate to "Group Policy Editor" and follow the path 

```
Local Computer Policy > Computer Configuration > Adinistrative Templates > Network > DNS Client
```

Under this menu Turn OFF **"Multicast Name Resolution"**

![[disable llmnr.png]]

##### 2. ENABLE NETWORK ACCESS CONTROL (NAC)
- NAC stands for Network Access Control. 
- It means that proper security controls are enabled at each open port. 
- It check each connection made to a port and ensure that MAC address of connected device is allowed inside.
- So anyone cant just go and connect to an open port to access the internal network.

##### 3. STRONG PASSWORD POLICY
- The company should implement strong password policy which ensures all users are using a strong password with at least 14 characters and limit the common words usage.
- Because Longer the password takes longer time crack.


