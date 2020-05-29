+++ 
title = "Connect to Autonomous Database in the Cloud with SQL Developer and SQLcl"
description = ""
date = 2020-01-17T16:59:40-06:00
series = ["Get Going With Autonomous Database in the Cloud"]
categories = ["cloud"]
tags = ["Oracle Autonomous Database", "Oracle Autonomous Data Warehouse", "OCI"]
+++
### Connect with SQL Developer Web 
![](https://res.cloudinary.com/tobyblog/image/upload/v1579364810/img/Screenshot_from_2020-01-09_17.11.52.jpg)

If you don't have SQL Developer installed, or you just want a quick and easy way to work with your new autonomous database, you can get started right away with SQL Developer Web, available from the Service Console. SQL Developer Web is a scaled down version of the desktop tool that allows you to query and enter data, run scripts, compile database objects, and monitor and administrate your database.

*[To get to the Service Console, first [Sign in to Oracle Cloud](https://www.oracle.com/cloud/sign-in.html) if you haven't already, and from the hamburger menu in the upper-left corner, choose "Autonomous Data Warehouse" or "Autonomous Transaction Processing", depending on the type of database you created. If necessary, select the appropriate Compartment, and you should see a list of databases. Click on the one you're interested in, and you should get to a screen like the one shown above, the Details page for your Autonomous Database.]*

Click on Service Console.

![](https://res.cloudinary.com/tobyblog/image/upload/v1579303667/img/Screen_Shot_2020-01-17_at_5.27.37_PM.jpg)

From here you can monitor usage and activity in your database (Overview and Activity), reset your ADMIN password (Administration), create resource management rules (Administration), and connect to and develop applications against your database (Development).

![](https://res.cloudinary.com/tobyblog/image/upload/v1579365886/img/Screen_Shot_2020-01-18_at_10.44.34_AM.jpg)

Click on Development, then click on SQL Developer Web.

![](https://res.cloudinary.com/tobyblog/image/upload/v1579365998/img/Screen_Shot_2020-01-18_at_10.46.30_AM.jpg)

If this is your first time using SQL Developer Web, enter ADMIN for username and the password you provided when setting up Autonomous Database.

That's it! From here you can create new schemas, new tables and views, load data, run scripts, and run queries just as you would from the desktop version of the tool. A simple Data Modeler is also provided where you can save and load diagrams, and generate and run DDL. Using SQL Developer Web is a great option, even for folks with more serious intended use cases than just setting up a transitory test environment. It has a simplified but powerful set of features that can get the job done. And if you have a larger team that requires connectivity, there's no worry about rolling out a new toolset or downloading credentials files. You can be productive immediately.

### Connect From a Local Instance of SQL Developer

However, if you already have a development tool like SQL Developer or SQLcl installed, and you want to integrate connectivity to your Autonomous Cloud Database into your existing workflows, then follow these instructions to get connected.

![](https://res.cloudinary.com/tobyblog/image/upload/v1579367350/img/dbconnect.jpg)

Navigate to the Details screen for your Autonomous Database or Autonomous Data Warehouse. Click on DB Connection.

![](https://res.cloudinary.com/tobyblog/image/upload/v1579367467/img/Screenshot_from_2020-01-09_16.23.01.jpg)

Now click on Download Wallet and save the file to your local disk. Now you're ready to connect! SQL Developer makes connecting to your new cloud database a breeze.

![](https://res.cloudinary.com/tobyblog/image/upload/v1578694991/img/Screenshot_from_2020-01-10_16.20.49.jpg)

From the Connections window, create a new connection.

![](https://res.cloudinary.com/tobyblog/image/upload/v1578695134/img/Screenshot_from_2020-01-10_16.25.12.jpg)

For Authentication Type, choose Default. Your username should be 'admin'. Enter the password you provided during the database configuration process. For Connection Type, choose Cloud Wallet, and simply point to the wallet file you downloaded earlier. You do not have to decrompress the file. Test your connection and save. That's it.

### Connect with SQLcl

If you're using a command line client like SQLcl, you will likely need to extract the contents of the wallet file (the ZIP file you just downloaded), and place them in your TNS_ADMIN diretory. If you don't have a TNS_ADMIN directory, create an environmental parameter TNS_ADMIN and point to the directory where the expanded contents of the wallet are located. Here is what is included in the wallet file.

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

Take a look at the tnsnames.ora file, and you'll see something like this:

![](https://res.cloudinary.com/tobyblog/image/upload/v1579367810/img/Screen_Shot_2020-01-18_at_11.16.39_AM.jpg)

You'll notice that *three* TNS entries were created for your database, `_high`, `_medium`, and `_low`. [These represent the priority](https://docs.oracle.com/en/cloud/paas/autonomous-data-warehouse-cloud/user/connect-predefined.html#GUID-9747539B-FD46-44F1-8FF8-F5AC650F15BE) given (or resources allocated) to processes connecting to those services. Mostly you'll want to use the `_high` connection, unless you plan to have lower priority users or background processes connecting to Autonomous Database.

![](https://res.cloudinary.com/tobyblog/image/upload/v1578695737/img/Screenshot_from_2020-01-10_16.33.11.jpg)

Since I'm running in Bash shell, I added the parameter declaration in my .bash_profile script, and after sourcing the script (i.e. `$ . ~/.bash_profile`), I am able to log in to my database using SQLcl.

![](https://res.cloudinary.com/tobyblog/image/upload/v1578696605/img/Screenshot_from_2020-01-10_16.49.36.jpg)

Success!
