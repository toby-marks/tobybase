+++
draft       = false
date        = "2013-07-24T21:42:00+00:00"
lastmod     = "2017-02-22"
title       = "How to Use ps to List Top Memory, CPU Eating Processes"
categories  = ["shell scripting"]
tags        = ["unix/linux"]
+++
The `ps` command is a versatile and configurable way to look at the processes running on your system, though I've generally gotten by with a lazy `ps -ef` to find out whatever I needed to know. Recently, though, a client's server began running out of memory, and as a number of processes were running on the server, the culprit was not immediately clear. I wanted to provide them with a quick, simple diagnostic tool they could use the next time they received an alert. 

If you look at the man page for `ps` you'll find a number of options available for sorting the list of processes returned as well as for filtering the columns displayed.

![](/images/2013-07-24-linux-unix-use-ps-to-list-top-memory-cpu-eating-processes/ac6287b65af17b95d247ea063f0aa1bfc6f106f3b69adf0ae73ed8258d3a6c7b.png)

Some examples of `ps` options, from RHEL 5.6:

**-e**  
select all processes

**-a**  
select all "child" processes

**-U &lt;user&gt;**  
select all processes for a specified user

**-p &lt;pid&gt;**  
select by process id

**-f**  
full-format listing; adds columns and shows process arguments

**-o**  
user-defined format

Many others are available, of course, but in order to keep it simple for the client I wanted to show the top 10 processes in terms of memory utilization, with the pid, memory usage, and process name displayed. Here is what I came up with:

```bash
$ ps -eo pid,user,pmem,args O R | tail -n 10
```
    
And here is an explanation:

**ps**  
shows running processes

**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-e**  
show all processes

**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-o pid,user,poem,args**  
show the pid, %mem, and process columns

**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;O R**  
sort by memory usage (ascending)

**tail**  
shows only the last lines

**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-n 10**  
show the last 10 lines

I liked the concept so much that I decided to adapt it for OS X, where the `ps` command takes a slightly different set of arguments. To my .bash_profile file I added the following lines:

```bash
alias psmem="while :; do clear; ps -ecm -o %mem,rss,pid,user,state,args | head -n 10; sleep 5; done"
alias pscpu="while :; do clear; ps -ecr -o %cpu,pid,user,state,args | head -n 10; sleep 2; done"
```

I have one alias for retrieving my top memory-utilizing processes and one for my top CPU-utilizing processes. The `while` loop in the alias implements a functionality similar to that of the Linux `watch` command, clearing the screen and running the process indefinitely until it is forcibly quit. For my own purposes I wanted to display some additional columns, like username and process state. I also changed the sort ordering to display the largest processes at the top of the list.

![](/images/2013-07-24-linux-unix-use-ps-to-list-top-memory-cpu-eating-processes/2bcd3b2c40d8bc37893b47399aed325508e95c655492de8866a8365eede7008f.png)
