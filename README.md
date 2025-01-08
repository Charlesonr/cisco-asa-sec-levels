# Cisco Firewall ASA - Security-levels 🧱🔥

Hi Folks, I’ll provide a small example of configuring the ASA firewall for security levels in three types of networks using Cisco Packet Tracer.

![Firewall ASA img](https://github.com/user-attachments/assets/8d7fab7f-c02c-4a29-82cb-406a8cede718)

It is a representation of how the ASA is configured to establish security levels for different parts of a network infrastructure, which is why I didn’t create a complete network with routers and all devices fully configured. (And be careful with copying projects, little student—do it yourself!😁 )

Let's go to the configuration!

## Initial Firewall Configuration

In this example, I will be using the ASA 5506-X.
After connecting all devices, let's access the ASA CLI and configure the hostname.

```
ciscoasa>enable
Password: 
ciscoasa#conf t
ciscoasa(config)#hostname FIREWALL-ASA
```

Done! Now our firewall has its hostname configured.

As you can see, the pre-configured password was empty, so we need to set a general password.

```
FIREWALL-ASA(config)#enable password Trust123
```

Password set! Now, we need to configure a username and password on the ASA.

```
FIREWALL-ASA(config)#username Admin password TechAdm
```

An important step is also configuring the clock and date. This can be done as follows:

```
FIREWALL-ASA(config)#clock set ?

configure mode commands/options:
  hh:mm:ss  Current Time

FIREWALL-ASA(config)#clock set 18:15:40 07 January 2025

```

## Interface Configuration and Security Levels

With these configurations in place, we can now configure our interfaces.

The ASA 5506-X has a total of 8 interfaces for connection.

![image](https://github.com/user-attachments/assets/755ffa58-1e5f-414a-ac62-b2a5c73429df)


To make it easier, I used the interfaces GigabitEthernet1/1, GigabitEthernet1/2, and GigabitEthernet1/3, respectively.

```
FIREWALL-ASA(config)#int gig1/1
FIREWALL-ASA(config-if)#no shutdown
```

In the CLI, you can abbreviate some commands, so `int gig1/1` is the same as writing `interface GigabitEthernet 1/1`.

The command `no shutdown` will basically activate the interface, as it is disabled by default.

Once the interface is selected, we add an IP range corresponding to the network we are configuring.

```
FIREWALL-ASA(config-if)#ip add 40.0.1.1 255.255.255.0
```

The name of the network in question will be OUTSIDE, as it is an external network. 🤓☝️

```
FIREWALL-ASA(config-if)#nameif OUTSIDE
INFO: Security level for "OUTSIDE" set to 0 by default.
```

By default, the interface comes with a security level set to zero.

The security level scale ranges from 0 to 100, where 0 represents traffic with no trust and 100 represents fully trusted traffic.

```
FIREWALL-ASA(config-if)#security-level 0
```

After that, just do the same with the other interfaces.

```
FIREWALL-ASA(config)#int gig1/3
FIREWALL-ASA(config-if)#no shut
FIREWALL-ASA(config-if)#ip add 192.168.50.1 255.255.255.0
#nameif INSIDE
INFO: Security level for "INSIDE" set to 0 by default.
FIREWALL-ASA(config-if)#security-level 100
FIREWALL-ASA(config)#write memory

FIREWALL-ASA(config)#int gig1/2
FIREWALL-ASA(config-if)#no shut

FIREWALL-ASA(config-if)#ip add 10.0.1.1 255.255.255.0
FIREWALL-ASA(config-if)#nameif DMZ 
INFO: Security level for "DMZ" set to 0 by default.
FIREWALL-ASA(config-if)#security-level 50
```

The "**DMZ**" network stands for "Demilitarized Zone 🪖", which is a perimeter network that protects the internal Local Area Network (LAN) of an organization from untrusted traffic.  

For this reason, this zone is assigned a security level of 50.

After the configurations, we need to save these settings to the firewall's memory and then check if everything is correct.

```
FIREWALL-ASA(config-if)#write memory
Building configuration...
Cryptochecksum: 07da110a 05e6113a 1ff45657 6d6e1396

1258  bytes copied in 1.929 secs (652 bytes/sec)
[OK]
```
```
FIREWALL-ASA#sh start
: Saved
: Written by enable_15 at 18:48:40 UTC jan 7 2025
: Call-home enabled from prompt by enable_15 at 18:48:40 UTC jan 7 2025
:
ASA Version 9.6(1)
!
hostname FIREWALL-ASA
enable password /AVjsweNOnuG7E7E encrypted
names
!
interface GigabitEthernet1/1
 nameif OUTSIDE
 security-level 0
 ip address 40.0.1.1 255.255.255.0
 shutdown
!
interface GigabitEthernet1/2
 nameif DMZ
 security-level 50
 ip address 10.0.1.1 255.255.255.0
!
interface GigabitEthernet1/3
 nameif INSIDE
 security-level 100
 ip address 192.168.50.1 255.255.255.0
!

```

Great! Our ASA is now configured!🧱🔥
