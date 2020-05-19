+++
draft       = false
date        = "2012-07-20T16:32:00+00:00"
lastmod     = "2017-02-22"
title       = "How To Fix High CPU Usage by SQL Developer on OS X"
categories  = ["Tools", "Database"]
tags        = ["oracle", "sql developer", "macos", "utilities"]
+++
I've been fighting for a couple of years now with an annoying performance bug in SQL Developer for OS X. After using the application for a while CPU usage would spike to around 100%, even when idle. This is a known issue associated with the "Look and Feel" preference. 

![](/img/2012-07-20-easy-fix-for-high-cpu-usage-in-sql-developer-on-os-x/b5b8f447d6dc433b4253c4814df5746e70e6e1e85235b391c9fef8b19e9f84d8.jpg)

Basically, don't use the "Mac OS X" setting. Instead, use the "Oracle" setting. The theme doesn't matter.

![](/img/2012-07-20-easy-fix-for-high-cpu-usage-in-sql-developer-on-os-x/aa9173adfc0090dc8b5b7fb471547c26a5243ca8f21c670580cd41df9cbd263f.jpg)

That fixes the CPU issue, but somewhat annoyingly moves the menu out of the menu bar at the top of the screen and into the application window, a la Windows, making the interface inconsistent with pretty much every other app I use on my Mac. [Until recently](https://forums.oracle.com/forums/thread.jspa?threadID=1980688&start=15&tstart=30), that is. The big news is that *as of SQL Developer 3.1, the menu is restored to the top of the screen* when using the Oracle Look and Feel. So for those who had been holding out (like myself) with the more native LaF, you can make the switch now and avoid the most annoying part of the performance/usability compromise.

![](/img/2012-07-20-easy-fix-for-high-cpu-usage-in-sql-developer-on-os-x/cfa32760a6b880df4f2b44e0c1db8a5155ee4c59233b761517416ef7f5c602f7.jpg)
