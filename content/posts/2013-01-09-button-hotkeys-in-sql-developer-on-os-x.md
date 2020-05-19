+++
draft       = false
date        = "2013-01-09T19:09:45+00:00"
lastmod     = "2017-02-22"
title       = "Button Hotkeys in SQL Developer on OS X"
categories  = ["Tools", "Database"]
tags        = ["oracle", "sql developer", "macOS", "utilities"]
+++
In Windows, most applications provide menu and button accelerator keys that allow you to control the application without having to use a mouse. Java Swing apps like SQL Developer allow you to do the same, ostensibly in a cross-platform compatible manner. 

One place I use this frequently is the Enter Binds dialog window that pops up when running a query using bind variables. 

![](/img/2013-01-09-button-hotkeys-in-sql-developer-on-os-x/f953827dc3e74e57df43fd0e48ff22b40ce119efaa74ce3712fd989522b02a6f.png)

In Java 6 on OS X, the hotkey for pressing the Help and Apply buttons was OPTION-H and OPTION-A, respectively.

I initially thought this capability had been broken in Java 7 on OS X, but I discovered today that it has been merely changed to CONTROL-OPTION-H and CONTROL-OPTION-A. 

Does anyone know if this is a configurable property of the application or Java virtual machine? If possible, I'd like to set it back to the simpler 1.6 way.