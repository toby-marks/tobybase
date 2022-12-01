+++ 
title = "Method for extracting tokens from a delimited string in SQL"
description = "There are several ways to extract tokens from a delimited string in Oracle SQL. Here I present one method with distinct advantages."
date = 2022-08-29T10:38:47-05:00
series = []
categories = ["SQL"]
tags = ["Oracle", "database"]
+++

There are several ways to extract tokens from a delimited string in Oracle SQL. The most basic uses the `INSTR` and `SUBSTR` functions.

Suppose you wanted to extract the third token from this simple period (`.`) delimited string: `one.two.three.four.five` 

You could do something like the following.

```sql
SELECT SUBSTR(
			the_string,
			INSTR(the_string, '.', 1, 2) + 1,
			INSTR(the_string, '.', 1, 3) - INSTR(the_string, '.', 1, 2) - 1
		)
  FROM (
           SELECT 'one.two.three.four.five' as the_string FROM dual
       )
``` 