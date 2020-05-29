+++
draft       = false
date        = "2012-05-11T20:54:00+00:00"
lastmod     = "2017-02-22"
title       = "ODBC Connection Fails for MS Access App on Citrix "
categories  = ["Citrix"]
tags        = ["ODBC", "Microsoft Access", "Microsoft Office", "Microsoft Windows", "Oracle"]
+++
I ran into an interesting problem recently when attempting to publish an MS Access application with [Citrix XenApp](http://www.citrix.com/English/ps2/products/product.asp?contentID=186&ntref=footer). The application pulled data from an Oracle database, so an ODBC connection was created on the Citrix server. What we found was that when a non-privileged user tried to run the application, the connection failed. When the Citrix server admin ran it, however, it worked. Furthermore, as long as the admin was logged in, anybody else could run the application successfully; when he logged out, the users' connections broke again.

Since Access is not my area of expertise, we finally contacted Microsoft tech support after trying a few suggestions we dug up in our own research. The support analyst clued us in to the following solution, which I pulled from an Oracle Primavera [setup document](http://docs.oracle.com/cd/E16688_01/Technical_Documentation/Terminal_Services_and_Citrix/Terminal%20Services%20and%20Citrix.pdf). 

From what I've seen on different support forums, this seems to be a pretty standard setup task when running Oracle on Windows over Citrix.

> If using an Oracle database platform, confirm [that] the Create Global Objects Windows Security Policy is set up on the Terminal Servers and add the domain Users or Groups of the end-users who will be accessing the published application. If the local security policy is not setup, the following steps can be used to add the policy:
> 
> 1. Click Start, Programs, Administrative Tools, and then click Local Security Policy.
> 1. Expand Local Policies, and then click User Rights Assignments.
> 1. In the right pane, double-click Create Global Objects.
> 1. In the Local Security Policy Setting dialogbox, click Add.
> 1. In the Select Users or Group dialog box, select the user group that these end users are a part of, click Add, and then click OK.
> 1. Click OK.
>
> If using an Oracle database platform, the Oracle client software also requires that you give the Authenticated User Group, Read and Execute privilege to the Oracle Home Directory and ALL its sub directories and files on the Terminal Servers.
