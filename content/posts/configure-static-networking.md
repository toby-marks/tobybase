+++ 
title = "How to Configure Static Networking in a Linux VM"
description = ""
date = 2020-05-20T16:36:06-05:00
categories = ["Linux"]
tags = ["virtual machines", "Docker", "unix/linux"]
+++
I originally had this requirement after creating a new persistent linux virtual machine in [OVM](https://www.oracle.com/virtualization/technologies/vm/) that needed static addressing. This is just standard linux setup, though, and can be applied to both virtual and physical machines.

## Turn on Networking

```
# sudo vi /etc/sysconfig/network
```

Make sure networking is enabled and configure the hostname.

```
NETWORKING=yes
HOSTNAME=myhost.mycompany.local
```

## Configure the Network Interface

```
# sudo vi /etc/sysconfig/network-scripts/ifcfg-eth0
```

Here is a sample config for static IPs.

``` 
HWADDR=00:30:48:56:A6:2E
TYPE=Ethernet
BOOTPROTO=none
IPADDR=192.168.1.10
PREFIX=24
GATEWAY=192.168.1.0
DNS1=192.168.1.254
DNS2=8.8.8.8
DNS3=8.8.4.4
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
# Disable IPV6
IPV6INIT=no
NAME=eth0
# This is system specific and can be created using 'uuidgen eth0' command
UUID=41171a6f-bce1-44de-8a6e-cf5e782f8bd6
DEVICE=eth0
ONBOOT=yes
```

## Configure DNS Servers

I also edit /etc/resolv.conf and setup DNS servers there. TBH, I'm not sure if this is necessary or redundant with ipcgf-eth0 

```
# sudo vi /etc/resolv.conf
```

Configure similar to the following:

```
search yourcompany.com
nameserver 192.168.1.254
nameserver 8.8.8.8
nameserver 8.8.4.4
```

## Finally, Restart Networking

```
# sudo systemctl network restart
```

You can verify that networking is working properly with the following commands

```
# ifconfig eth0
# ip a s eth0
# ip r
# route -n
# ping 192.168.1.254
# ping google.com
```