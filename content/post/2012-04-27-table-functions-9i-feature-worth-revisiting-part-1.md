+++
draft       = false
date        = "2012-04-27T22:27:00+00:00"
lastmod     = "2017-02-22"
title       = "Table Functions: 9i Feature Worth Revisiting (Part 1)"
categories  = ["Splaining"]
tags        = ["oracle", "plsql", "pipelined table functions"]
+++
Yes, they've been around for a while, but if you haven't made use of table functions lately you might want to reacquaint yourself with this old gem. Table functions allow you to query the contents of PL/SQL collection types using SQL's TABLE operator. Here is a simple example.

```plpgsql
    CREATE OR REPLACE PACKAGE example AS

         TYPE nested_table_typ IS TABLE OF VARCHAR2(100);

         FUNCTION get_nested_table_data RETURN nested_table_typ PIPELINED;

    END example;
    /

    CREATE OR REPLACE PACKAGE BODY example AS

         FUNCTION get_nested_table_data RETURN nested_table_typ PIPELINED IS
         BEGIN
              PIPE ROW ('one');
              PIPE ROW ('two');
              PIPE ROW ('three');
              RETURN;
         END get_nested_table_data;

    END example;
    /
```

The package function example.get_nested_table_data returns a PL/SQL collection — in this case, a nested table. The PIPELINED directive instructs the function to return data to the calling query as rows are populated by the PIPE ROW command. That way, you don't need to wait for the function to finish before you get results from your query.

The function can be invoked from the SELECT clause of a query in the usual way, and will return a valid result.

```sql
    SELECT example.get_nested_table_data FROM dual;

    GET_NESTED_TABLE_DATA
    -------------------------------
    SCHEMA.SYS_PLSQL_166672_9_1(one,two,three)
```

This is possible because nested tables and VARRAYs are supported as native types in SQL. The other type of PL/SQL collection, associative arrays, are not. Therefore you cannot use an associative array as the return type of a pipelined table function. However, it's pretty trivial to create a wrapper function that translates an associative array into a nested table. You wouldn't necessarily take this approach in your own code, but it comes in handy when you're transforming data coming from a third party package or function.

But what if you need the contents of the collection presented as a table, with one record per entry in the collection? This is where the TABLE operator comes in handy.

```sql
    SELECT column_value FROM TABLE(example.get_nested_table_data);

    COLUMN_VALUE
    -------------------
    one
    two
    three
```

The TABLE operator casts a collection type as an aggregated result set, meaning it allows you to query it as if it were a table.

Why would you need to do this?

One reason might be because you want to query or join data retrieved from a web service, or from an LDAP server. Another reason might be to simplify a complex query with lots of unions, case statements, and subqueries by fetching, manipulating, and combining your data inside a PL/SQL function. You can leverage the ease and maintainability of building your collection in a procedural manner, while still having access to the collection data from SQL. There is some overhead associated with that approach, though, so you'll want to be careful about applying it in performance-critical situations. Still, though, it's worth considering when the complexity of SQL constructs gets out of hand.
