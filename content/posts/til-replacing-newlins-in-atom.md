+++ 
title = "TIL: How to Replace Newline in Atom"
description = ""
date = 2020-06-05T17:23:31-05:00
series = ["TIL"]
categories = ["Tools"]
tags = ["Atom"]
+++
Newline characters are matched as `\r\n` in Atom. To remove all newlines from a file or selection of text, do a regex search for `\r\n` and replace with nothing.
