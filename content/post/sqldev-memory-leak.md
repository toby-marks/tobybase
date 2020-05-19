+++ 
date 		= 2019-05-01T16:47:39-05:00
title 		= "Fix High CPU Usage / Memory Leak in SQL Developer 18+ for Windows"
description = "How to fix an annoying problem in the latest versions of SQL Developer"
categories  = ["Tools", "Database"]
tags        = ["oracle", "macos", "sql developer", "utilities", "java"]
+++
If you run SQL Developer on Windows and experience high CPU usage and/or memory leak issues that freeze your PC, then you might want to try disabling some features that are likely causing the problem. 

I have personally observed this behavior on every version of SQL Developer since 18.2. Only my PC laptop is affected; my Macbook installation runs fine. I use the prepackaged Java 8 JDK. I have several colleagues who run the same configuration on Windows with no issues at all, so go figure.

![Disable Completion Insight in SQL Developer](https://res.cloudinary.com/tobyblog/image/upload/v1556748201/img/image001.png)

The "fix" is to disable all Completion Insight features from the Preferences panel, as shown above. Since making that change I've not encountered any further problems. So if you're having trouble, give it a shot. If your experience varies, or if you have better insight into the cause of the problem, let me know.