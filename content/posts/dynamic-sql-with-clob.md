+++ 
title 		= "An Example of Dynamic SQL Using a CLOB"
description = ""
date 		= 2020-05-22T13:28:39-05:00
series 		= []
categories 	= ["SQL & PL/SQL"]
tags 		= ["Oracle"]
+++
You might not know this, but you can use the `EXECUTE IMMEDIATE` command with a CLOB, the same as with a VARCHAR2 variable.

```sql
declare
  l_sql clob;
  l_str1 varchar2(32767);
  l_str2 varchar2(32767);
begin
  dbms_lob.createtemporary(l_sql, false);

  l_str1 := 'some large SQL chunk';
  l_str2 := 'and the rest of large SQL chunk';

  l_sql := l_str1;
  dbms_lob.writeappend(l_sql, length(l_str2), l_str2);

  execute immediate l_sql;

  dbms_lob.freetemporary(l_sql);

end;
```
