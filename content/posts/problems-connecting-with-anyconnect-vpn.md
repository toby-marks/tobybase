+++ 
title 		= "Error in Cisco AnyConnect Client: \"Cannot Confirm it is Connected\""
description = "What to do when you experience this error when attempting to connect to your company's Cisco VPN: AnyConnect cannot confirm it is connected to your secure gateway. The local network may not be trustworthy. Please try another network."
date = 2020-01-27T15:20:10-06:00
series 		= []
categories  = ["Cisco"]
tags        = ["macOS", "VPN", "tools"]
+++
*[NOTE: problem identified on my personal laptop with macOS 10.15.2, Cisco AnyConnect Secure Mobility Client 4.6.03049]*
### What to Do When You Encounter This Error
![](https://res.cloudinary.com/tobyblog/image/upload/v1580160888/img/not_trustworthy.jpg)

> AnyConnect cannot confirm it is connected to your secure gateway.  The local network may not be trustworthy.  Please try another network.

This is a potentially misleading explanation, as the problem in my case was due to a corrupted VPN certificate. 

To fix the problem on macOS, do the following.

1.  Open Keychain.app and search for a certificate for your VPN. If you already have one, export it to create a backup in case you need to restore, then delete the certificate in Keychain.

2.  Point a browser to https://vpn.yourcompany.com, or whatever the address of VPN happens to be.

3.  The certificate should automatically download to Keychain at this point. If the certificate is self-signed, you will get a warning. If that is the case, you will need to download the certificate yourself (click on Advanced… / View Certificate or something similar in your browser of choice). Once downloaded, double clicking the certificate should open it in Keychain.app.

![](https://res.cloudinary.com/tobyblog/image/upload/v1580161931/img/Screen_Shot_2020-01-27_at_3.17.26_PM.jpg)

4.  View your certificate in Keychain. Expand the `Trust` section and select `Always Trust`. Save your Keychain.

Now try connecting once again from AnyConnect, and hopefully that fixes your problem.