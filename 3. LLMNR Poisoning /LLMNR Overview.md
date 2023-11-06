# LLMNR Poisoning 

## LLMNR POISONING OVERVIEW

LLMNR stands for Link Local Multicast Name Resolution. The primary usage of LLMNR is to identify and resolve hosts when DNS fails to do so. 
LLMNR was previously known as NBT-NS.

A major flaw in LLMNR is that when made a proper request, it responds with the users username and NTLMv2 password Hash of that user when properly responded to. 
So these hashes are critical because an attacker can use these to login to the system through Pass the Hash or crack it using tools like hashcat. 

## ATTACK METHODOLOGY

LLMNR Poisoning is typically a Man in the Middle attack where the attacker sits in the middle and responds to the LLMNR requests.
As told before LLMNR is used if DNS fails to resolve something.
1. The attacker sits in the middle
2. When the VICTIM makes a DNS request to the server to resolve something and DNS fails to resolve, LLMNR is the next choice.
3. When there is no response from DNS, Now the VICTIM broadcasts LLMNR request to every other system on the network.
4. The attacker responds to this request using a tool like "Responder" and the LLMNR in the victim system will send a response containing the victim's USERNAME and NTLMv2 HASH.

## POTENTIAL IMPLICATIONS

A Successful LLMNR Poisoning attack will result in leaking username and NTLMv2 password hash of the users on the domain. 
If an attacker has this information he can either login to the other systems using techniques like Pass the Hash or they can crack the hash and obtain the cleartext credentials.  

## LAB DEMONSTRATION:

To poison LLMNR we need to use a tool called "Responder"
"Responder" will poison the LLMNR requests and listens for any events to occur.

1. Start responder tool with the following Syntax
```bash
responder -I eth0 -dwv
```
-I : used to denote Interface name
-d : enables answers for DHCP requests
-v : enables verbosity

![responder1](https://github.com/ab3lsec/ADAttackDefenseProject/assets/87868050/28776206-2a62-4dd2-9cad-a9b0ea848bc1)
![responder2](https://github.com/ab3lsec/ADAttackDefenseProject/assets/87868050/e3124e95-ed26-4b00-a5a1-0eb8db28d9ae)


2. Once started, responder will start listening for any event to occur on the network - that means anyone to request a host that cannot be resolved by DNS. So lets go to "BATMAN" and make a fake request to trigger an event.

![fakeserver](https://github.com/ab3lsec/ADAttackDefenseProject/assets/87868050/f3601353-84aa-49a0-bdfb-73078ee6f621)


In this case "fakeserver" is not a host and DNS will fail to resolve, Now the victim will look to resolve using LLMNR.
Once the victim uses the LLMNR, the "Responder" tool will respond for request and capture the username and NTLMv2 Hash of the victim.

![hash llmnsr](https://github.com/ab3lsec/ADAttackDefenseProject/assets/87868050/4c218e7d-baf9-44bf-98a3-ef2607d26140)


We have successfully obtained the username (`HERO\batman`) and the NTLMv2 hash of the domain user "Bruce Wayne".
We can crack this NTLMv2 hash using `hashcat` with the following syntax:

```
hashcat -m 5600 ntlmhash.txt rockyou.txt --force
```

![crackpass](https://github.com/ab3lsec/ADAttackDefenseProject/assets/87868050/33f777ce-ba33-4665-b9ff-a7854dbfda62)


## LLMNR POISONING DEFENSE TECHNIQUES

Now through these lab we understood that turning ON LLMNR feature is a bad security practise. 
So here are some defences to prevent LLMNR poisoning attack:

##### 1. DISABLE LLMNR FEATURES
To disable it navigate to "Group Policy Editor" and follow the path 

```
Local Computer Policy > Computer Configuration > Adinistrative Templates > Network > DNS Client
```

Under this menu Turn OFF **"Multicast Name Resolution"**

![disable llmnr](https://github.com/ab3lsec/ADAttackDefenseProject/assets/87868050/33203fe9-303f-4337-bdf8-ad9e30d5217e)


##### 2. ENABLE NETWORK ACCESS CONTROL (NAC)
- NAC stands for Network Access Control. 
- It means that proper security controls are enabled at each open port. 
- It check each connection made to a port and ensure that MAC address of connected device is allowed inside.
- So anyone cant just go and connect to an open port to access the internal network.

##### 3. STRONG PASSWORD POLICY
- The company should implement strong password policy which ensures all users are using a strong password with at least 14 characters and limit the common words usage.
- Because Longer the password takes longer time crack.



