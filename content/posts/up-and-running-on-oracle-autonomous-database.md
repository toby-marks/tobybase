+++ 
title = "Get Up and Running Fast On Oracle Autonomous Database in the Cloud"
description = "How to launch and connect to free instances of Oracle Autonomous Database and Oracle Autonomous Data Warehouse in the cloud."
date = 2020-01-09T16:58:15-06:00
series = []
categories = ["Database", "Cloud"]
tags = ["Oracle Autonomous Database", "Oracle Autonomous Data Warehouse", "Oracle Cloud"]
draft = true
+++
![](https://res.cloudinary.com/tobyblog/image/upload/v1578611791/img/Screenshot_from_2020-01-09_17.15.20.jpg)

With Autonomous Database and Autonomous Data Warehouse, Oracle is promising to leverage the power of AI and machine learning to automate upgrade, maintenance, and tuning operations in the database - vastly reducing the drudgery of DBA work and freeing up human minds to add more value to their companies. As a developer, you don't have to wait to start experimenting with these exciting new features. They are available now for free on Oracle Cloud for small-scale testing and learning.

In this blog I'll show you how to get started with Autonomous Database in a matter of minutes.

![](https://res.cloudinary.com/tobyblog/image/upload/v1578613738/img/Screenshot_from_2020-01-09_17.48.03.jpg)

The first thing you need to do is [sign up for an Oracle Cloud account](https://myservices.us.oraclecloud.com/mycloud/signup). Enter your account details, which includes name, address, phone, and a credit card Oracle keeps on file just in case you should want to upgrade to a paid account. If you stick to the 'Always Free' services, however, your account should never be charged.

![](https://res.cloudinary.com/tobyblog/image/upload/v1578614479/img/Screenshot_from_2020-01-09_13.06.36.jpg)

When you're all done you should be presented with a clean looking dashboard from which you can launch your Autonomous Transaction Processing (ATP) or Autonomous Data Warehouse (ADW) instance. The free tier offers one of each.

Click on the button (or panel) labelled 'Create an ATP database', for example. 

![](https://res.cloudinary.com/tobyblog/image/upload/v1578694026/img/Screenshot_from_2020-01-10_16.06.51.jpg)

Enter a name for your database or accept the default values. The workload type should default to Transaction Processing since we chose to create an ATP database. 

![](https://res.cloudinary.com/tobyblog/image/upload/v1578614301/img/Screenshot_from_2020-01-09_13.08.57.jpg)
 
*Be sure to click that radio button during the setup process to avoid accidental charges!*

![](https://res.cloudinary.com/tobyblog/image/upload/v1578694291/img/Screenshot_from_2020-01-09_13.11.08.jpg)

It will only take a minute or two for Oracle to provision and start your database instance, and then you're ready to connect.

![](https://res.cloudinary.com/tobyblog/image/upload/v1578694525/img/Screenshot_from_2020-01-09_17.11.52.jpg)

When the database is started the label changes from orange to green, and you can click on the DB Connection button just to the right of the green label. 

![](https://res.cloudinary.com/tobyblog/image/upload/v1578694559/img/Screenshot_from_2020-01-09_16.23.01.jpg)

Click on Download Wallet and save the file. Now you're ready to connect!

If you already have a tool like Oracle SQL Developer, connecting to your new cloud database is a breeze.

![](https://res.cloudinary.com/tobyblog/image/upload/v1578694991/img/Screenshot_from_2020-01-10_16.20.49.jpg)

From the Connections window, create a new connection.

![](https://res.cloudinary.com/tobyblog/image/upload/v1578695134/img/Screenshot_from_2020-01-10_16.25.12.jpg)

For Authentication Type, choose Default. Your username should be 'admin'. Enter the password you provided during the database configuration process. For Connection Type, choose Cloud Wallet, and simply point to the wallet file you downloaded earlier. You do not have to decrompress the file. Test your connection and save. That's it.

If you're using a command line client like SQLcl, you will likely need to expand the contents of the wallet file (the ZIP file you just downloaded), and place them in your TNS_ADMIN diretory. If you don't have a TNS_ADMIN directory, create an environmental parameter TNS_ADMIN and point to the directory where the expanded contents of the wallet are located. Here is what is included in the wallet file.

```
toby@michael:~$ cd $TNS_ADMIN
toby@michael:~/oracle/admin/atpsb$ ll
total 64
drwxrwxr-x 2 toby toby  4096 Jan  9 16:42 ./
drwxrwxr-x 4 toby toby  4096 Jan 10 16:48 ../
-rw-rw-r-- 1 toby toby  6733 Jan  9 16:38 cwallet.sso
-rw-rw-r-- 1 toby toby  6688 Jan  9 16:38 ewallet.p12
-rw-rw-r-- 1 toby toby  3276 Jan  9 16:38 keystore.jks
-rw-rw-r-- 1 toby toby   301 Jan  9 16:38 ojdbc.properties
-rw-rw-r-- 1 toby toby   114 Jan  9 16:38 sqlnet.ora
-rw-rw-r-- 1 toby toby  1791 Jan  9 16:38 tnsnames.ora
-rw-rw-r-- 1 toby toby  3336 Jan  9 16:38 truststore.jks
-rw-rw-r-- 1 toby toby 20050 Jan  9 16:38 Wallet_ATPSB.zip
```

As you can see it not only contains the tnsnames.ora and sqlnet.ora files normally used to connect to a local database, but also certificate and wallet files required to open and maintain a secure connection to your database in the cloud.

![](https://res.cloudinary.com/tobyblog/image/upload/v1578695737/img/Screenshot_from_2020-01-10_16.33.11.jpg)

Since I'm running in Bash shell, I added the parameter declaration in my .bash_profile script, and after sourcing the script (i.e. `$ . ~/.bash_profile`), I am able to log in to my database using SQLcl.

![](https://res.cloudinary.com/tobyblog/image/upload/v1578696605/img/Screenshot_from_2020-01-10_16.49.36.jpg)

Success!

### Connecting with Oracle Instant Client

Some people may need (or simply want to use) a true Oracle SQL\*Plus environment - for executing complicated SQL\*Plus batch scripts, for instance, that won't run properly in SQLcl. You can hunt for an appropriate platform-native version, or perhaps even build a virtual machine with [VirtualBox](http://www.virtualbox.org/) or some similar tool in order to run a specific version of Instant Client that you require. A far easier (and faster)  way to get Instant Client, in my opinion, is to use [Docker](http://www.docker.com/).   
 
*I'll skip the steps for installing Docker on this blog. You can find instructions on the Docker website [or here](https://lmgtfy.com/?q=how+to+install+docker). For those unfamiliar with Docker or container technologies, you can think of containers as a lighter-weight version of virtual machines; whereas a virtual machine attempts to emulate hardware in software, a container seeks to emulate only the O/S or application service layer needed by a specific applicatoin. As a result, containers use far less memory and CPU than software running in virtual machines.*


 


