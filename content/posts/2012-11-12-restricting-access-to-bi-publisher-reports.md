+++
draft       = false
date        = "2012-11-12T18:12:00+00:00"
lastmod     = "2017-02-22"
title       = "How To Restrict Access to BI Publisher Reports"
categories  = ["Reporting"]
tags        = ["Oracle", "BI Publisher"]
series      = ["BI Publisher"]
+++
In [my last post]({{< relref "2012-11-12-configure-oracle-bi-publisher-to-authenticate-with-active-directory.md" >}})  I described how to configure BI Publisher to authenticate against an LDAP server, typically a corporate Active Directory server. Aside from authentication, you can also use LDAP group membership to determine who has access to a given report. Here's how you do it.

1. Create a group in your LDAP/AD directory and assign your target users to that group.
2. Revisit your LDAP authentication settings. These are located on the Admin Tab, on the Security Configuration page. In particular, look at the value for Group Search Filter.

    e.g.

    ```
    (&(objectClass=group)(|(CN=XMLP\*)(CN=DEPT_GROUP_\*)))
    ```

    In case you don't recognize it, this is an LDAP query string. This query string determines which groups BI Publisher "sees", or recognizes, when setting up report access. The groups returned by this query will determine the set from which you can choose when configuring which groups see which reports. 

    Here is a deconstruction of the query string:

    **(&** _Logical AND over the following conditions_

    **(objectClass=group)** _Return LDAP groups, as opposed to users or computers, etc._

    **(|** _Logical OR over the following conditions_

    **(CN=XMLP\*)** _Return groups whose name starts with "XMLP"_

    **(CN=DEPT\_GROUP\_\*)** _Return groups whose name starts with "DEPT\_GROUP\_"_

    A quick primer on LDAP query syntax [can be found here.](http://wiki.pentaho.com/display/ServerDoc2x/LDAP+Search+Filter+Syntax)

3. Modify this query string to include the group you just created.
4. Bounce BI Publisher.
5. Log in to BI Publisher as an administrator. Go to the Admin tab, and then to the Roles and Permissions page.
6. Click on the role/group you just created, and using the shuttle control on the next page, select which reports your new group should have access to. If necessary, you may need to revoke permissions on your report from other groups, or move your report to the top level of the report folder hierarchy in order to segregate access appropriately.