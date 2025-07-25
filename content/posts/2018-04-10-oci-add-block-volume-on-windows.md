+++
title = "Access Denied Attaching Block Volume to Windows VM in OCI"
date = "2018-04-10"
description = "What to do when you get an access denied permissions error when attempting to attach a block volume to a Windows virtual machine in Oracle Cloud"
categories = ["cloud"]
tags = ["Oracle", "OCI", "IaaS", "Microsoft Windows", "virtual machines"]
+++
<img src="/images/2018-04-10-oci-add-block-volume-on-windows/access-denied.jpg" width="800px">

If you are getting the above error when trying to add a block volume to a Windows VM in Oracle Cloud Infrastructure, the solution is quite simple.
<!--more-->

I ran across this problem recently when configuring a Windows virtual machine on Oracle Cloud for the first time. I needed some additional storage, so I added a SCSI block volume that gave me what I required.

<img src="/images/2018-04-10-oci-add-block-volume-on-windows/attached-block-volumes.jpg" width="800px">

After attaching the block volume to the compute instance, I clicked on the elipsis to the right of the volume overview panel, and selected *iSCSI Commands & Information* as shown.

<img src="/images/2018-04-10-oci-add-block-volume-on-windows/iscsi-commands.jpg" width="586px">

In order to make use of your attached volume, the commands shown in the *Attach Commands* pane must be run from a Powershell or Command terminal, after which you can format and mount the volume according to the [online instructions provided by Oracle](https://docs.us-phoenix-1.oraclecloud.com/Content/Block/Tasks/connectingtoavolume.htm).

<img src="/images/2018-04-10-oci-add-block-volume-on-windows/powershell-button.jpg" width="369px">

However, when I clicked on the Powershell terminal icon in the Windows Taskbar and attempted to run the iSCSI commands, I got the following error:

	Set-Service: : Service 'Microsoft iSCSI Initiator Service (msiscsi)' cannot be configured due to the following error: Access is denied

The problem is that the iSCSI commands Oracle provides need to be run from a privileged terminal, or "elevated shell".

<img src="/images/2018-04-10-oci-add-block-volume-on-windows/run-as-admin.jpg" width="800px">

Search for Powershell. Right-click on the icon, and choose *Run as administrator*. You will now be able to successfully run the iSCSI commands and connect to your block volume. If you don't care for that method, [there are a number of other ways](https://www.top-password.com/blog/5-ways-to-run-powershell-as-administrator-in-windows-10/) to launch an elevated shell in Windows. 

Have fun experimenting with OCI. There is no better cost/performance value for cloud infrastructure. 