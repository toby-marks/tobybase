+++
draft       = false
date        = "2012-04-05T21:19:05+00:00"
lastmod     = "2017-02-22"
title       = "How To Make Bash Scripts crontab-Compatible"
categories  = ["Unix"]
tags        = ["bash", "cron", "scripting", "unix/linux"]
+++
When writing shell scripts in bash, consider whether your script can be automated in a batch scheduler like cron. If you reference any external resources in your script relative to the directory in which it resides, those references may break when running your script through cron.

By default, crontab runs scripts in the user's home directory, as specified in `/etc/passwd`. For user "Tim" with home directory `/home/tim`, a sample crontab might look something like the following:

```bash
0 0 * * * runme.sh
```

Every night at midnight, cron will try to run the file `/home/tim/runme.sh`. However, if `runme.sh` is actually in a sub-directory under `~tim`, you will need to tell cron how to reach the file. One option would be to qualify the path in crontab, like so:

```bash
0 0 * * * ~/bin/runme.sh
```

Now cron can find the file. However, if the script makes references to other files inside of `/bin` in a path-relative way, those references will be broken.

```bash
#!/bin/bash
#runme.sh
./runthis.sh      # can't find this file when running under cron
./runthat.sh      # can't find this file when running under cron
```

That's because to cron `./` is not the directory in which the script is running, but rather whatever is defined as the base directory; in the default case, the user's home directory.

It is possible to specify the base directory in the crontab file itself by defining the special variable `HOME`.

```bash
HOME=/home/tim/bin
0 0 * * * runme.sh
```

However, you may not always have control over how your crontab is configured. Perhaps a system administrator is in charge of all the schedules on your server. Or you may organize your scripts in several different directories.

An easy solution is to start your bash scripts with the following command:

```bash
cd $(dirname $0)
```

The program `dirname` returns the directory portion of the fully qualified path name sent to it. The `$0` argument in this context is name of the running script. Basically, this code ensures that the current working directory is the same as the directory in which your script is running. That way all your path-relative references are correct whether running manually or through a scheduler.
