+++
draft       = false
date        = "2012-11-12T17:32:00+00:00"
lastmod     = "2017-02-22"
title       = "How To Configure Oracle BI Publisher to Authenticate With Active Directory"
categories  = ["Tools", "Database"]
tags        = ["bi publisher", "active directory", "ldap", "oracle"]
series      = ["BI Publisher"]
+++
+ Log in to BI Publisher as an administrator.
+ Click on the Admin tab.
+ Click on Security Configuration.
+ For Security Model, choose LDAP and enter the following parameters:

    Setting                                         | Example Value
    ------------------------------------------------|-------------------------------------------------------------
    URL                                             | ldap://ad.mycompany.com:389  <cite style="color:red; font-size:smaller">Your company's LDAP server and port.</cite>
    Administrator Username                          | CN=ADMIN,OU=Users,OU=Shared Services,DC=mycompany,DC=com  <cite style="color:red; font-size:smaller">The fully qualified DN of the account you use to sign in to LDAP.</cite>
    Administrator Password                          | myxlplyx  <cite style="color:red; font-size:smaller">The password for the above account.</cite>
    Distinguished Name for Users                    | DC=mycompany,DC=com  <cite style="color:red; font-size:smaller">The "root" level at which to search for users.</cite>
    Distinguished Name for Groups                   | OU=Global Groups,DC=mycompany,DC=com  <cite style="color:red; font-size:smaller">The "root" level at which to search from groups.</cite>
    Group Search Filter                             | (&(objectClass=group)(|(CN=XMLP*)(CN=DEPT_GROUP_*)))  <cite style="color:red; font-size:smaller">LDAP query string defines which groups are relevant to BI Publisher.</cite>
    Group Attribute Name                            | cn
    Group Member Attribute Name                     | member
    Member Of Group Attribute Name                  |
    Group Description Attribute Name                | description
    JNDI Context Factory Class                      | com.sun.jndi.ldap.LdapCtxFactory
    Group Retrieval Page Size	                    |
    attribute used for RDN                          | sAMAccountName  <cite style="color:red; font-size:smaller">Allows users to log in to BI Publisher using their corporate AD account name.</cite>
    Automatically clear LDAP cache                  | Checked
    Ldap Cache Interval                             | 1
    Ldap Cache Interval Unit                        | Hour
    Default User Group Name                         |
    Attribute Names for Data Query Bind Variables   |
<br>
+ Bounce BI Publisher. 
+ Try logging in with your corporate AD credentials. Remember, you need to be assigned to one of the following special groups in Active Directory if you want developer or admin privileges in BI Publisher:

XMLP_ADMIN  
XMLP_DEVELOPER  
XMLP_SCHEDULER  
XMLP_ANALYZER_EXCEL  
XMLP_ANALYZER_ONLINE  
XMLP_TEMPLATE_DESIGNER

You can read more about those roles and what they do [in the Oracle Documentation](http://docs.oracle.com/cd/E10383_01/doc/bip.1013/b40017/T421739T475591.htm).