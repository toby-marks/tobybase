+++ 
title = "3 Tips for Running SAS Queries on Oracle"
description = "Some helpful tips for boosting the performance of SAS queries on Oracle database."
date = 2019-09-23T13:54:50-05:00
categories = ["SQL"]
tags = ["Oracle", "SAS", "tuning"]
+++
I don't know a whole lot about SAS, but on my current gig I am frequently called upon to tune queries that run in that environment. Here are three things I have learned to keep in mind as a SAS developer when running against Oracle database.

### Use PRESERVE_COMMENTS

This is most critical. In Oracle, SQL hints are specified in a specially formatted inline comment. Running a query or DML statement without those hints could be detrimental to performance.

To retain these hints in SAS you will need to add the *Preserve_Comments* option to the CONNECT statement. Then any hints you add to your queries will be passed through to the Oracle optimizer.
 
Here's an example:
 
```
Proc Sql;
 
Connect to Oracle (Uid = XXXX PW = 'XXXX' Path = MyConnection Preserve_Comments);
 
Create Table A1 as Select * From Connection To Oracle (
	SELECT /*+ PARALLEL(4) */ * FROM myschema.mytable
);

Disconnect From Oracle;

Quit;                              
```
 
Restriction: This option is valid only in the explicit SQL pass-through method, not in the implicit libname method. For more details on implicit and explicit connection methods please click here

### Use ReadBuff to Optimize Fetches

*ReadBuff* is another connection option that can be used to enhance query performance, especially when running queries against Oracle Exadata. It allows you to specify the number of rows that are held in memory for input into SAS. Just be careful not to set this too high, or you may run out of memory.

A value of `ReadBuff = 1` means that one row is fetched from the database at a time. For large queries this can be very inefficient. If you don't specify a value for ReadBuff, the number of rows returned is set to the default, which can be anywhere from one row to a "best guess" estimate based on the row length of your data. It is recommended that users start with a buffer size of 5000, increasing or decreasing in increments of 500 until you determine the optimum buffer size for your purposes.

If you set this number too high (e.g. one million records) you can grind your system to a halt as your server struggles to retrieve all that data over the network and load it into memory. It's better to start with smaller increments of no more than 100,000, depending on the size of your records.
 
For example:

```
Libname MyLIB Oracle Uid=XXXX PW='XXXX' Path=MyConnection ReadBuff=100000;
 
Data A1;
Set myschema.mytable;
Run;
 
Proc Sql;

Connect to Oracle (Uid = ‘XXXX’ PW = 'XXXX' Path = MyConnection ReadBuff=100000);

Create Table A1 as Select * From Connection To Oracle (
	SELECT * FROM myschema.mytable
);

Disconnect From Oracle;

Quit;
```

### Use InsertBuff to Optimize DML

InsertBuff works much the same way as ReadBuff. It uses memory to cache rows so that you can insert many records into Oracle at a time, as opposed to executing many single row inserts. As a general rule of thumb we tended to use `InsertBuff = 100000`. The SAS engine calculates how many rows it can insert at one time. The same caveats apply about not setting this value too high; just make sure you're not using up all your available memory. And test to optimize the setting for your purposes and hardware configuration.

An example of using InsertBuff:

```
Libname MyLIB Oracle Uid=XXXX[My_Group_Space] PW='XXXX' Path=MyConnection InsertBuff=100000;
 
Data My_Group.Test1;
Set myschema.mytable;
Run;
 
Proc Sql;

Create Table My_Group.Test1 as
	SELECT * FROM myschema.mytable;
 
Quit;
```

Hope these tips help some of you. If it did, let me know.
