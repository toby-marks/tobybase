+++
draft       = false
date        = "2012-07-02T22:40:00+00:00"
lastmod     = "2017-02-22"
title       = "How To Use a Perl CGI Script to Show Real Web Server Host Name"
categories  = ["Perl"]
tags        = ["CGI", "unix/linux"]
+++
Here is a short and simple Perl CGI script that you can use to expose the hostname of your web server, as opposed to just the server/domain portion of the URL string:

```perl
    #!/usr/bin/perl
    
    print "Content-type: text/html\n\n";
    print `hostname`;
    
    1;
```

Name the file something like `hostname.cgi` and place it in your web server's `/cgi-bin/` directory, or if your web server is configured appropriately, just name it `hostname.pl` and drop it wherever you like. 

The following script can be used to dump all the CGI environment variables.

```perl
    #!/usr/bin/perl
    
    print "Content-type: text/html\n\n";
    print "<font size=+1>Environment</font>\n";
    foreach (sort keys %ENV)
    {
      print "<b>$_</b>: $ENV{$_}<br>\n";
    }
    
    1;
```

Note that this includes `SERVER_NAME`, but what that really gives you is the "server" portion of the URL string. In other words, if your URL is http:&#47;&#47;www.google.com&#47;, then the `SERVER_NAME` CGI environment variable would be "www.google.com". Fine, but the first script can be used to identify the actual server being called, for example, when your page is hosted on multiple servers load-balanced under a virtual domain name.

Of course, you could combine the two scripts to give you everything:

```perl
    #!/usr/bin/perl
    
    print "Content-type: text/html\n\n";
    print "<font size=+1>Environment</font>\n";
    foreach (sort keys %ENV)
    {
      print "<b>$_</b>: $ENV{$_}<br>\n";
    }
    
    print "<b>REAL SERVER HOSTNAME:</b> ",`hostname`;
    
    1;
```
