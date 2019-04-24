+++
draft       = false
date        = "2013-07-19T22:32:00+00:00"
lastmod     = "2017-02-22"
title       = "How to Fix SQL Developer on a Retina Display Mac(book)"
categories  = ["Mac", "Tools", "Oracle Database"]
tags        = ["oracle", "macos", "sql developer", "utilities", "java"]
+++
I just purchased a new Macbook Pro 13-inch with Retina Display and was horrified to see how fuzzy SQL Developer looked on the high-res screen. Thanks to Thunderforge on the [Stack Overflow forums](http://stackoverflow.com/questions/15181079/apple-retina-display-support-in-java-jdk-1-7-for-awt-swing) for this tip:

> Java 7u40 Early Access now supports HiDPI (Retina) displays, as 
> shown by this [changelog](http://download.java.net/jdk7u40/changes/jdk7u40-b28.html?q=download/jdk7u40/changes/jdk7u40-b28.html). So retina support is now available on Java 6, Java 7u40 or higher, and Java 8.

I am currently running the [Early Adopter release of SQL Developer 4.0](http://www.oracle.com/technetwork/developer-tools/sql-developer/downloads/sqldev-download-v4-1925679.html) which requires JDK 1.7. I'm not sure what the state of Java 8 is on the Mac, so I opted to upgrade my JDK from 1.7.0_25 to 1.7.0_40. After that I had nice, sharp lines back in SQL Developer and all my other Java apps. 

Thanks to Oracle's Java team for addressing this!