+++ 
title = "'Invalid Format' Error Connecting to Server with SSH and Private Key"
description = ""
date = 2020-06-06T12:01:34-05:00
series = []
categories = ["Linux"]
tags = ["unix/linux"]
+++
If you get an 'invalid format' error when trying to SSH in to a remote server using a private key file that you know is valid, it may be because the key was generated with PuTTY.

![](https://res.cloudinary.com/tobyblog/image/upload/v1591463072/img/puttygen05.jpg)

If the key begins with `PuTTY-User-Key-File` and you are using SSH/openSSH (ie. command line SSH) to connect to your server, you will need to first convert the key file using the `puttygen` tool that comes with PuTTY. 

![](https://res.cloudinary.com/tobyblog/image/upload/v1591463071/img/puttygen02.jpg)

Launch `puttygen` and load your existing private key file. From the menu, select *Export OpenSSH key (force new file format)*. Then click *Save private key* and save your converted key file.

![](https://res.cloudinary.com/tobyblog/image/upload/v1591463071/img/puttygen04.jpg)

Examine the new key file. The format should begin with BEGIN OPENSSH PRIVATE KEY and look something like the picture shown above. You should now be able to log in to your server using your new key file.