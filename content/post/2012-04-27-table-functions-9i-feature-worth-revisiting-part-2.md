+++
draft       = false
date        = "2012-04-27T22:35:00+00:00"
lastmod     = "2017-02-22"
title       = "Table Functions: 9i Feature Worth Revisiting (Part 2)"
categories  = ["Splaining"]
tags        = ["oracle", "plsql", "pipelined table functions"]
+++
In [part one of this series]({{< relref "2012-04-27-table-functions-9i-feature-worth-revisiting-part-1.md" >}}-1) I showed a simple example of how table functions can be used to treat PL/SQL collection types as tables in a SQL query. We used a nested table collection type to which we manually added a few entries. In this example, I intend to show a more practical use of table functions using all three PL/SQL collection types: associative arrays, nested tables, and VARRAYs.

Let's revisit a use case I alluded to in my earlier blog entry: incorporating LDAP data into a query. Suppose your task is to code a custom authorization scheme for an enterprise application reporting system. You want row access to be determined by a user's group memberships in the company's Active Directory server. If a user is assigned to "the Company A group" in Active Directory, he gets to see Company A's data. You can configure that kind of restriction at a database level using [Oracle's Virtual Private Database](http://www.oracle.com/technetwork/database/security/index-088277.html) feature. Configuring VPD is beyond the scope of this blog entry, but ultimately you will be associating a table to a portion of a SQL WHERE clause that gets appended to every query written against that table. You can find more documentation on VPD [on the OTN site](http://download.oracle.com/docs/cd/B28359_01/network.111/b28531/vpd.htm). In this case, let's assume you know that another team has written a PL/SQL database package to query data from Active Directory, and you want to make use of that code. The only problem is that all the functions in that package return collections of various types, and you need to be able to access that information from the WHERE clause you are building with VPD. This is where table functions come in handy.

Let's begin by setting up our example schema. We'll be looking at client sales data. First, let's set up our clients.

```sql
    CREATE TABLE clients (
      client_id NUMBER,
      client_name VARCHAR2(50));
      
    INSERT INTO clients VALUES (100, 'Timco Industries');
    INSERT INTO clients VALUES (200, 'Moneybros');
    INSERT INTO clients VALUES (300, 'The Monacle Corporation');
    INSERT INTO clients VALUES (400, 'Schmapple');
```

We also need application users who will be logging in to view the report data. We'll assume each application user is uniquely linked to a user in Active Directory via the external_id column.

```sql
    CREATE TABLE users (
      sequence_number NUMBER,
      user_name VARCHAR2(20),
      external_id VARCHAR2(20),
      first_name VARCHAR2(30),
      last_name VARCHAR2(30));
      
    INSERT INTO users VALUES (1, 'JACKA', 'jaugusti', 'Jack', 'Augustine');
    INSERT INTO users VALUES (2, 'KENKI', 'kkidd', 'Ken', 'Kidd');
    INSERT INTO users VALUES (3, 'BRIAH', 'bhill', 'Brian', 'Hill');
    INSERT INTO users VALUES (4, 'KEOVO', 'kvongkas', 'Keo', 'Vongkaseum');
```

Finally, we need a data table on which to report.

```sql
    CREATE TABLE quarterly_sales_data (
      sequence_number NUMBER,
      client_id NUMBER,
      sales_year NUMBER,
      sales_quarter NUMBER,
      sales_amount NUMBER);
      
    INSERT INTO quarterly_sales_data VALUES (1, 100, 2011, 1, 20000);
    INSERT INTO quarterly_sales_data VALUES (2, 100, 2011, 2, 17500);
    INSERT INTO quarterly_sales_data VALUES (3, 100, 2011, 3, 33000);
    INSERT INTO quarterly_sales_data VALUES (4, 200, 2011, 1, 4500);
    INSERT INTO quarterly_sales_data VALUES (5, 200, 2011, 2, 7000);
    INSERT INTO quarterly_sales_data VALUES (6, 200, 2011, 3, 6300);
    INSERT INTO quarterly_sales_data VALUES (7, 300, 2011, 1, 11100);
    INSERT INTO quarterly_sales_data VALUES (8, 300, 2011, 2, 12800);
    INSERT INTO quarterly_sales_data VALUES (9, 300, 2011, 3, 14000);
    INSERT INTO quarterly_sales_data VALUES (10, 400, 2011, 1, 55600);
    INSERT INTO quarterly_sales_data VALUES (11, 400, 2011, 2, 80000);
    INSERT INTO quarterly_sales_data VALUES (12, 400, 2011, 3, 78500);
```

Our basic report query will look something like this:

```sql
    SELECT client_name, sales_year, sales_quarter, sales_amount
      FROM quarterly_sales_data NATURAL JOIN clients;
```
  
Our Active Directory groups will be TIMCO_GRP, MONEYBROS_GRP, MONACLE_GRP, and SCHMAPPLE_GRP. If a user belongs to one or more of these groups, he has permission to view the respective client's data.

Now we need a function that returns data from Active Directory. Just to keep it simple we will create a package that hardcodes the group memberships and returns them as collections of various types. We will then look at how table functions can be used to transform each one of those collections into an aggregate result set. (NOTE: querying LDAP directories from PL/SQL is not too difficult, and might serve as the basis for a future blog entry.)

Here's our function. We've placed it in a package called LDAP_UTIL.

```sql
    CREATE OR REPLACE PACKAGE ldap_util AS
    
      TYPE ldap_entry_typ IS RECORD (
        cn VARCHAR2(30),
        description VARCHAR2(1000),
        distinguished_name VARCHAR2(200),
        name VARCHAR2(30),
        samaccountname VARCHAR2(30));
        
      TYPE ldap_groups_nt IS TABLE OF ldap_entry_typ;
      TYPE ldap_groups_aa IS TABLE OF ldap_entry_typ INDEX BY PLS_INTEGER;
      TYPE ldap_groups_va IS VARRAY(100) of ldap_entry_typ;
      
      FUNCTION get_user_groups_nt (p_user VARCHAR2) RETURN ldap_groups_nt;
      FUNCTION get_user_groups_aa (p_user VARCHAR2) RETURN ldap_groups_aa;
      FUNCTION get_user_groups_va (p_user VARCHAR2) RETURN ldap_groups_va;
      
    END ldap_util;
    /
    SHOW ERRORS
    
    CREATE OR REPLACE PACKAGE BODY ldap_util AS
      
      FUNCTION get_user_groups_nt (p_user VARCHAR2) RETURN ldap_groups_nt IS
        l_groups ldap_groups_nt := ldap_groups_nt();
      BEGIN
        CASE p_user
          WHEN 'jaugusti' THEN
    
            l_groups.extend;
            l_groups(1).cn := 'TIMCO_GRP';
            l_groups(1).description := 'Employees working on the Timco Industries account.';
            l_groups(1).distinguished_name := 'CN=TIMCO_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'TIMCO_GRP';
            l_groups(1).samaccountname := 'TIMCO_GRP';
    
          WHEN 'kkidd' THEN
    
            l_groups.extend;
            l_groups(1).cn := 'MONEYBROS_GRP';
            l_groups(1).description := 'Employees working on the Moneybros account.';
            l_groups(1).distinguished_name := 'CN=MONEYBROS_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'MONEYBROS_GRP';
            l_groups(1).samaccountname := 'MONEYBROS_GRP';
    
          WHEN 'bhill' THEN
    
            l_groups.extend;
            l_groups(1).cn := 'TIMCO_GRP';
            l_groups(1).description := 'Employees working on the Timco Industries account.';
            l_groups(1).distinguished_name := 'CN=TIMCO_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'TIMCO_GRP';
            l_groups(1).samaccountname := 'TIMCO_GRP';
    
            l_groups.extend;
            l_groups(2).cn := 'MONACLE_GRP';
            l_groups(2).description := 'Employees working on the Monacle Corporation account.';
            l_groups(2).distinguished_name := 'CN=MONACLE_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(2).name := 'MONACLE_GRP';
            l_groups(2).samaccountname := 'MONACLE_GRP';
    
          WHEN 'kvongkas' THEN
    
            l_groups.extend;
            l_groups(1).cn := 'MONEYBROS_GRP';
            l_groups(1).description := 'Employees working on the Moneybros account.';
            l_groups(1).distinguished_name := 'CN=MONEYBROS_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'MONEYBROS_GRP';
            l_groups(1).samaccountname := 'MONEYBROS_GRP';
    
            l_groups.extend;
            l_groups(2).cn := 'MONACLE_GRP';
            l_groups(2).description := 'Employees working on the Monacle Corporation account.';
            l_groups(2).distinguished_name := 'CN=MONACLE_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(2).name := 'MONACLE_GRP';
            l_groups(2).samaccountname := 'MONACLE_GRP';
    
            l_groups.extend;
            l_groups(3).cn := 'SCHMAPPLE_GRP';
            l_groups(3).description := 'Employees working on the Schmapple account.';
            l_groups(3).distinguished_name := 'CN=SCHMAPPLE_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(3).name := 'SCHMAPPLE_GRP';
            l_groups(3).samaccountname := 'SCHMAPPLE_GRP';
    
        END CASE;
        RETURN l_groups;
      END get_user_groups_nt;
      
      FUNCTION get_user_groups_aa (p_user VARCHAR2) RETURN ldap_groups_aa IS
        l_groups ldap_groups_aa;
      BEGIN
        CASE p_user
          WHEN 'jaugusti' THEN
    
            l_groups(1).cn := 'TIMCO_GRP';
            l_groups(1).description := 'Employees working on the Timco Industries account.';
            l_groups(1).distinguished_name := 'CN=TIMCO_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'TIMCO_GRP';
            l_groups(1).samaccountname := 'TIMCO_GRP';
    
          WHEN 'kkidd' THEN
    
            l_groups(1).cn := 'MONEYBROS_GRP';
            l_groups(1).description := 'Employees working on the Moneybros account.';
            l_groups(1).distinguished_name := 'CN=MONEYBROS_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'MONEYBROS_GRP';
            l_groups(1).samaccountname := 'MONEYBROS_GRP';
    
          WHEN 'bhill' THEN
    
            l_groups(1).cn := 'TIMCO_GRP';
            l_groups(1).description := 'Employees working on the Timco Industries account.';
            l_groups(1).distinguished_name := 'CN=TIMCO_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'TIMCO_GRP';
            l_groups(1).samaccountname := 'TIMCO_GRP';
    
            l_groups(2).cn := 'MONACLE_GRP';
            l_groups(2).description := 'Employees working on the Monacle Corporation account.';
            l_groups(2).distinguished_name := 'CN=MONACLE_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(2).name := 'MONACLE_GRP';
            l_groups(2).samaccountname := 'MONACLE_GRP';
    
          WHEN 'kvongkas' THEN
    
            l_groups(1).cn := 'MONEYBROS_GRP';
            l_groups(1).description := 'Employees working on the Moneybros account.';
            l_groups(1).distinguished_name := 'CN=MONEYBROS_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'MONEYBROS_GRP';
            l_groups(1).samaccountname := 'MONEYBROS_GRP';
    
            l_groups(2).cn := 'MONACLE_GRP';
            l_groups(2).description := 'Employees working on the Monacle Corporation account.';
            l_groups(2).distinguished_name := 'CN=MONACLE_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(2).name := 'MONACLE_GRP';
            l_groups(2).samaccountname := 'MONACLE_GRP';
    
            l_groups(3).cn := 'SCHMAPPLE_GRP';
            l_groups(3).description := 'Employees working on the Schmapple account.';
            l_groups(3).distinguished_name := 'CN=SCHMAPPLE_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(3).name := 'SCHMAPPLE_GRP';
            l_groups(3).samaccountname := 'SCHMAPPLE_GRP';
    
        END CASE;
        RETURN l_groups;
      END get_user_groups_aa;
      
      FUNCTION get_user_groups_va (p_user VARCHAR2) RETURN ldap_groups_va IS
        l_groups ldap_groups_va := ldap_groups_va();
      BEGIN
        CASE p_user
          WHEN 'jaugusti' THEN
    
            l_groups.extend;
            l_groups(1).cn := 'TIMCO_GRP';
            l_groups(1).description := 'Employees working on the Timco Industries account.';
            l_groups(1).distinguished_name := 'CN=TIMCO_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'TIMCO_GRP';
            l_groups(1).samaccountname := 'TIMCO_GRP';
    
          WHEN 'kkidd' THEN
    
            l_groups.extend;
            l_groups(1).cn := 'MONEYBROS_GRP';
            l_groups(1).description := 'Employees working on the Moneybros account.';
            l_groups(1).distinguished_name := 'CN=MONEYBROS_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'MONEYBROS_GRP';
            l_groups(1).samaccountname := 'MONEYBROS_GRP';
    
          WHEN 'bhill' THEN
    
            l_groups.extend;
            l_groups(1).cn := 'TIMCO_GRP';
            l_groups(1).description := 'Employees working on the Timco Industries account.';
            l_groups(1).distinguished_name := 'CN=TIMCO_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'TIMCO_GRP';
            l_groups(1).samaccountname := 'TIMCO_GRP';
    
            l_groups.extend;
            l_groups(2).cn := 'MONACLE_GRP';
            l_groups(2).description := 'Employees working on the Monacle Corporation account.';
            l_groups(2).distinguished_name := 'CN=MONACLE_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(2).name := 'MONACLE_GRP';
            l_groups(2).samaccountname := 'MONACLE_GRP';
    
          WHEN 'kvongkas' THEN
    
            l_groups.extend;
            l_groups(1).cn := 'MONEYBROS_GRP';
            l_groups(1).description := 'Employees working on the Moneybros account.';
            l_groups(1).distinguished_name := 'CN=MONEYBROS_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(1).name := 'MONEYBROS_GRP';
            l_groups(1).samaccountname := 'MONEYBROS_GRP';
    
            l_groups.extend;
            l_groups(2).cn := 'MONACLE_GRP';
            l_groups(2).description := 'Employees working on the Monacle Corporation account.';
            l_groups(2).distinguished_name := 'CN=MONACLE_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(2).name := 'MONACLE_GRP';
            l_groups(2).samaccountname := 'MONACLE_GRP';
    
            l_groups.extend;
            l_groups(3).cn := 'SCHMAPPLE_GRP';
            l_groups(3).description := 'Employees working on the Schmapple account.';
            l_groups(3).distinguished_name := 'CN=SCHMAPPLE_GRP,OU=Global Groups,DC=acme,DC=com';
            l_groups(3).name := 'SCHMAPPLE_GRP';
            l_groups(3).samaccountname := 'SCHMAPPLE_GRP';
    
        END CASE;
        RETURN l_groups;
      END get_user_groups_va;
      
    END ldap_util;
    /
    SHOW ERRORS
```

As you can see, LDAP_UTIL has three functions that return the three kinds of collections in PL/SQL: nested tables, associative arrays, and varrays.

```sql
    FUNCTION get_user_groups_nt (p_user VARCHAR2) RETURN ldap_groups_nt;
    FUNCTION get_user_groups_aa (p_user VARCHAR2) RETURN ldap_groups_aa;
    FUNCTION get_user_groups_va (p_user VARCHAR2) RETURN ldap_groups_va;
```

The collections consist of a group of records representing "group" data from Active Directory.  

```sql
    TYPE ldap_entry_typ IS RECORD (
        cn VARCHAR2(30),
        description VARCHAR2(1000),
        distinguished_name VARCHAR2(200),
        name VARCHAR2(30),
        samaccountname VARCHAR2(30));
```

The p_user parameter would be the user's network id, corresponding to the sAMAccountName attribute in Active Directory; since we're hardcoding group data this is not important to us at the moment. The point is you pass in a username and you get a collection of groups.

Using the same technique we demonstrated in part one, we now create a wrapper package of pipelined functions to transform the collection data into something we can query - an aggregate result set.

```sql
    CREATE OR REPLACE PACKAGE ldap_util_pipelined AS
    
      FUNCTION get_user_groups_nt (p_user VARCHAR2) RETURN ldap_util.ldap_groups_nt PIPELINED;
      FUNCTION get_user_groups_aa (p_user VARCHAR2) RETURN ldap_util.ldap_groups_nt PIPELINED;
      FUNCTION get_user_groups_va (p_user VARCHAR2) RETURN ldap_util.ldap_groups_va PIPELINED;
      
    END ldap_util_pipelined;
    /
    SHOW ERRORS
    
    CREATE OR REPLACE PACKAGE BODY ldap_util_pipelined AS
    
      FUNCTION get_user_groups_nt (p_user VARCHAR2) 
      RETURN ldap_util.ldap_groups_nt PIPELINED IS
        l_groups ldap_util.ldap_groups_nt;
      BEGIN
        l_groups := ldap_util.get_user_groups_nt(p_user);
        FOR i IN 1 .. l_groups.count LOOP
          PIPE ROW (l_groups(i));
        END LOOP;
        RETURN;
      END get_user_groups_nt;
      
      FUNCTION get_user_groups_aa (p_user VARCHAR2) 
      RETURN ldap_util.ldap_groups_nt PIPELINED IS
        l_groups ldap_util.ldap_groups_aa;
      BEGIN
        l_groups := ldap_util.get_user_groups_aa(p_user);
        FOR i IN 1 .. l_groups.count LOOP
          PIPE ROW (l_groups(i));
        END LOOP;
        RETURN;
      END get_user_groups_aa;
      
      FUNCTION get_user_groups_va (p_user VARCHAR2) 
      RETURN ldap_util.ldap_groups_va PIPELINED IS
        l_groups ldap_util.ldap_groups_va;
      BEGIN
        l_groups := ldap_util.get_user_groups_va(p_user);
        FOR i IN 1 .. l_groups.count LOOP
          PIPE ROW (l_groups(i));
        END LOOP;
        RETURN;
      END get_user_groups_va;
    
    END ldap_util_pipelined;
    /
    SHOW ERRORS
```

Note the return types of the three functions in ldap_util_pipelined.

```sql
    FUNCTION get_user_groups_nt (p_user VARCHAR2) RETURN ldap_util.ldap_groups_nt PIPELINED;
    FUNCTION get_user_groups_aa (p_user VARCHAR2) RETURN ldap_util.ldap_groups_nt PIPELINED;
    FUNCTION get_user_groups_va (p_user VARCHAR2) RETURN ldap_util.ldap_groups_va PIPELINED;
```

The first two return a nested table collection, while the third returns a VARRAY. These two collections can be pipelined because they correspond to native SQL datatypes in Oracle; not so for associative arrays. In order to pipeline the associative array returned from ldap_util.get_user_groups_aa, we need to map that collection to one of the other types. Here, we map it to a nested table. The iterative logic is the same, though, as the other two functions.

Now we can verify that our approach worked by issuing some simple queries, supported by the SQL "TABLE" construct.

```sql
    SQL> select * from table(ldap_util_pipelined.get_user_groups_nt('jaugusti'));
    
    CN        DESCRIPTION                                        DISTINGUISHED_NAME                            NAME      SAMACCOUN
    --------- -------------------------------------------------- --------------------------------------------- --------- ---------
    TIMCO_GRP Employees working on the Timco Industries account. CN=TIMCO_GRP,OU=Global Groups,DC=acme,DC=com  TIMCO_GRP TIMCO_GRP
```

As you can see, the individual attributes of the record structure form the columns of our new table, and we can reference those columns directly, if we wish.

```sql
    SQL> select cn from table(ldap_util_pipelined.get_user_groups_nt('kvongkas'));
    
    CN
    ---------------
    MONEYBROS_GRP
    MONACLE_GRP
    SCHMAPPLE_GRP
```

All that's left for us at this point is to construct a WHERE clause condition to use with Oracle's Virtual Private Database feature to limit data returned from queries against the table QUARTERLY_SALES_DATA. That condition could look something like this, assuming that our application connects to the database as the logged in user.

```sql
    client_id IN (SELECT CASE cn
                           WHEN 'TIMCO_GRP' THEN 100
                           WHEN 'MONEYBROS_GRP' THEN 200
                           WHEN 'MONACLE_GRP' THEN 300
                           WHEN 'SCHMAPPLE_GRP' THEN 400
                         END
                    FROM TABLE(ldap_util_pipelined.get_user_groups_nt(USER)))
```

Here the mapping between AD group name and client ID is hardcoded in the CASE expression. A more flexible way would be to create a mapping table. Also, this is probably not a case study in writing a well performing VPD clause, but the real point here is that using SQL table functions and PL/SQL pipelined functions, we can translate data retrieved from an API, LDAP directory, or web service into table-like structures that we can query, join, and otherwise manipulate just like real tables.
