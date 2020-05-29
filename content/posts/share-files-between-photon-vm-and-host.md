+++ 
title = "How to Share Files Between a Photon OS VM and the VM Host"
description = ""
date = 2020-05-29T13:41:02-05:00
series = []
categories = ["linux", "Docker"]
tags = ["Photon OS", "VMWare", "containers", "virtual machines", "unix/linux"]
+++
(The following is adapted from the [official Photon OS FAQ.](https://vmware.github.io/photon/assets/files/html/1.0-2.0/Frequently-Asked-Questions.html#q-how-do-i-transfer-or-share-files-between-photon-and-my-host-machine))

## How do I transfer or share files between #Photon and my host machine?

Use `vmhgfs-fuse` to transfer files between Photon and your host machine:

1. Enable Shared folders in the Workstation or Fusion UI (edit the VM settings and choose Options->Enabled shared folders).
2. Make sure open-vm-tools is installed (it is installed by default in the Minimal installation and OVA import).
3. Run `vmware-hgfsclient` to list the shares. 
4. Next, do one of the following:

	* Mount shared folders:

	~~`vmhgfs-fuse .host:/$(vmware-hgfsclient) /mnt/hgfs`~~

	*(Note: The above doesn't work with more than one shared folder. Since I only had a handful, I just enumerated the ones I wanted to share.)*

	```
	mkdir /mnt/hgfs/share1 /mnt/hgfs/share2 /mnt/hgfs/share3
	vmhgfs-fuse .host:/share1 /mnt/hgfs/share1
	vmhgfs-fuse .host:/share2 /mnt/hgfs/share2
	vmhgfs-fuse .host:/share3 /mnt/hgfs/share3
	```  
	* or, add the following line(s) to `/etc/fstab` and reboot

	~~`.host:/ /mnt/hgfs fuse.vmhgfs-fuse <options> 0 0`~~

	*(Note: Likewise, enumerate the individual desired shares in the fstab file)*

	```
	.host:/share1 /mnt/hgfs/share1 fuse.vmhgfs-fuse <options> 0 0
	.host:/share2 /mnt/hgfs/share2 fuse.vmhgfs-fuse <options> 0 0
	.host:/share3 /mnt/hgfs/share3 fuse.vmhgfs-fuse <options> 0 0
	```
