+++
draft       = false
date        = "2012-08-27T20:48:08+00:00"
lastmod     = "2017-02-22"
title       = "How to Avoid the \"Job scheduling failed because the user has no permission to access this report\" Error in BI Publisher"
categories  = ["Tools", "Oracle Database"]
tags        = ["oracle", "bi publisher", "ldap", "bi", "reporting"]
+++
![](/img/2012-08-27-bi-publisher-job-scheduling-failed-because-the-user-has-no-permission-to-access-this-report/864dbb90dbf623eb4f36b26a3ecf315cfec7121cf33e16730b6060c6572dfb35.jpg)

You can get this error if you are trying to schedule a report and your BI Publisher installation uses LDAP authentication.

To avoid it, make sure that the "Administrator" account you use to connect to your LDAP server is in the XMLP_ADMIN group. The LDAP settings are located on the Admin tab, under Security Settings / Security Configuration.

![](/img/2012-08-27-bi-publisher-job-scheduling-failed-because-the-user-has-no-permission-to-access-this-report/c44997adb44d6687468d544341fcd25b818ebdd46087d1849c38e855ee874488.jpg)
