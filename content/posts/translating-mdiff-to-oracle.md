+++ 
title = "Translating Teradata's MDIFF Function to Oracle SQL"
description = "How to code the MDIFF function in Oracle SQL"
date = 2020-05-27T10:22:08-05:00
series = []
categories = ["SQL"]
tags = ["Oracle", "Teradata", "migrations"]
+++
Teradata [defines its MDIFF function](https://docs.teradata.com/reader/kmuOwjp1zEYg98JsB8fu_A/Q5tWBsRwZN7VBXQkzAoddQ) as follows:

> **MDIFF**
> 
> Returns the moving difference between the specified value expression for the current row and the preceding width rows for each row in the partition.

Here "the preceding width rows" means something like the preceding *n* rows, where n is sometimes interpreted as the sum of *all* of the preceding rows, but generally as the value of the last row.

Teradata's docs also has this to say about MDIFF:

> **Using SUM Instead of MDIFF**
>
> The use of MDIFF is strongly discouraged. It is a Teradata extension to the ANSI SQL:2011 standard, and is retained only for backward compatibility with existing applications. MDIFF(x, w, y) is equivalent to:
> 
> *x - SUM(x) OVER (ORDER BY y*
> 	*ROWS BETWEEN w PRECEDING AND w PRECEDING)*

MDIFF applies to `NUMBER` columns or `DATE` and the window *(w, y)* is typically defined as (1, 1), to specify only the value of column *x* in the preceding row. If this is the case, then MDIFF is simply **the delta between the current row and the previous row.**

That function can be simplified and rewritten in Oracle SQL as

```
x - LAG(x) OVER (ORDER BY y)
```

Or alternatively, if you want to break delta calculations on column *z*, then

```
x - LAG(x) OVER (PARTITION BY z ORDER BY y)
```

One last thing to consider is how you want to treat the edge cases, such as when there is no preceding row. Is the expected value NULL in that case, as it is by default, or do you want to treat the delta as zero? You need to understand how how this value is being used in the code you are converting — whether that value is being used in higher-level calculations, etc.

```
x - LAG(x, 1, 0) OVER (PARTITION BY z ORDER BY y)
x - LAG(x, 1, NULL) OVER (PARTITION BY z ORDER BY y)
```

