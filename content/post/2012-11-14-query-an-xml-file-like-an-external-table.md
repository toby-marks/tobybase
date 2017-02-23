+++
draft       = false
date        = "2012-11-14"
lastmod     = "2017-02-22"
title       = "How To Use SQL to Query an XML Document Using Oracle External Tables"
categories  = ["HowTos"]
tags        = ["oracle", "xml", "sql", "external tables"]
+++
Oracle's [external table feature](http://docs.oracle.com/cd/B28359_01/server.111/b28319/et_concepts.htm) allows you to treat structured files on the database server as tables for query and DML purposes. To use an example from the linked documentation, a tab-delimited file like this:

    56   november, 15, 1980  baker             mary       alice     09/01/2004
    87   december, 20, 1970  roper             lisa       marie     01/01/1999

...could be queried like this:

```sql
SELECT employee_number,
       employee_first_name,
       substr(employee_middle_name, 1, 1),
       employee_last_name,
       employee_hire_date,
       to_date(employee_dob,'month, dd, yyyy')
  FROM employees;
```

After defining the external table like this:

```sql
    CREATE TABLE employees
           (employee_number      CHAR(5),
            employee_dob         CHAR(20),
            employee_last_name   CHAR(20),
            employee_first_name  CHAR(15),
            employee_middle_name CHAR(15),
            employee_hire_date   DATE)
         ORGANIZATION EXTERNAL
           (TYPE ORACLE_LOADER
            DEFAULT DIRECTORY def_dir1
            ACCESS PARAMETERS
              (RECORDS DELIMITED BY NEWLINE
               FIELDS (employee_number      CHAR(2),
                       employee_dob         CHAR(20),
                       employee_last_name   CHAR(18),
                       employee_first_name  CHAR(11),
                       employee_middle_name CHAR(11),
                       employee_hire_date   CHAR(10) date_format DATE mask "mm/dd/yyyy"
                      )
              )
            LOCATION ('info.dat')
           );
```

Assuming of course that the file's name is `info.dat`, and its path on the filesystem has already been created as a directory object `DEF_DIR1` in the database.

A similar technique exists for querying simple XML files, taking advantage of [Oracle's XML DB](http://docs.oracle.com/cd/E11882_01/appdev.112/e23094/toc.htm) features. _HT to [odie_63](https://forums.oracle.com/forums/profile.jspa?userID=695787) on the XML DB forums for introducing me to this method._

Suppose you have a simple XML file (elements, but no attributes; no complex column types - it could work for the preceding, but I'm not sure) like so.

```xml
    <Employees>
        <Employee>
            <id>1234</id>
            <lastname>Jetson</lastname>
            <firstname>George</firstname>
            <department>10</department>
            <salary>50000.00</salary>
        </Employee>
        <Employee>
            <id>1235</id>
            <lastname>Crackorn</lastname>
            <firstname>James</firstname>
            <department>10</department>
            <salary>40000.00</salary>
        </Employee>
        <Employee>
            <id>1236</id>
            <lastname>Hoffman</lastname>
            <firstname>Burl</firstname>
            <department>20</department>
            <salary>75000.00</salary>
        </Employee>
    </Employees>
```

Place it on the database server in a readable directory, e.g. `/home/oracle`. Name it `employees.xml`.

Now execute the following in SQL*Plus:

```sql
    SQL> CREATE OR REPLACE DIRECTORY XML_DIR AS '/home/oracle';
    
    SQL> CREATE OR REPLACE VIEW employees_v AS
    SELECT *
    FROM XMLTable('/Employees/Employee'
            passing xmltype(
                     bfilename('XML_DIR','employees.xml')
                    , nls_charset_id('AL32UTF8')
                    )
            columns id           number path 'id'
                  , lastname     varchar2(30) path 'lastname'
                  , firstname    varchar2(30) path 'firstname'
                  , department   number path 'department'
                  , salary       number path 'salary'
           );

    SQL> SELECT * FROM employees_v;
    ID   LASTNAME FIRSTNAME DEPARTMENT SALARY
    ---- -------- --------- ---------- ------
    1234 Jetson   George    10         50000    
    1235 Crackorn James     10         40000
    1236 Hoffman  Burl      20         75000
```

You can modify construction of the view to read from multiple files by using a simple `UNION` operator, or you can make the source filename dynamic so you can switch datasets at runtime. Here's how you might do something like that:

```sql
    SQL> CREATE OR REPLACE VIEW employees_v AS
    SELECT *
    FROM XMLTable('/Employees/Employee'
            passing xmltype(
                     bfilename('XML_DIR', userenv('CLIENT_INFO'))
                    , nls_charset_id('AL32UTF8')
                    )
            columns id           number path 'id'
                  , lastname     varchar2(30) path 'lastname'
                  , firstname    varchar2(30) path 'firstname'
                  , department   number path 'department'
                  , salary       number path 'salary'
           );
```

Then at runtime, specify the file you want to use like this:

```sql
    SQL> exec dbms_application_info.set_client_info('employees_01.xml');
    
    SQL> SELECT * FROM employees_v;
    ID   LASTNAME FIRSTNAME DEPARTMENT SALARY
    ---- -------- --------- ---------- ------
    1234 Jetson   George    10         50000    
    1235 Crackorn James     10         40000
    1236 Hoffman  Burl      20         75000

    SQL> exec dbms_application_info.set_client_info('employees_02.xml');
    
    SQL> SELECT * FROM employees_v;
    ID   LASTNAME FIRSTNAME DEPARTMENT SALARY
    ---- -------- --------- ---------- ------
    1234 Ringwald Malory    30         63000    
    1235 Dulles   Bob       10         100000
    1236 Stein    Frank     50         55000
```

Why might this be useful? Suppose you have an arbitrary number of identically structured XML files in a directory that you want to import into a relational database table. Using a shell script you can, for each file in the directory, 

+ log in to SQL*Plus
+ set the `CLIENT_INFO` variable to the name of the current file
+ perform a simple insert into your table reading all records from the view

You might even create a cron job to monitor a particular directory for new XML files, processing them into tables and then deleting or archiving the files when done.