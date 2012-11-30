---
layout: default
title: How To Steal Java Web App Source Code From Apache Tomcat 6.0.16
tags : [hack, exploit, java, apache, tomcat]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

There I was minding my own business, browsing few sites when I got this 404 error:

    HTTP Status 404 - /innocent/doesnotexists

    type Status report
    message /innocent/doesnotexists
    description The requested resource (/innocent/doesnotexists) is not available.

    Apache Tomcat/6.0.16
    
I saw 404s all the time it's not even funny. Instinctively I just want to hit back, but then I saw `Apache Tomcat/6.0.16`. This particular version was released on 8th February 2008. 3 years ago. Interesting. Maybe, there's something we can do here. 

I'm not familiar with Apache Tomcat at all, and I don't code Java. Maybe it's a good time to learn then. First thing is just to search for Apache Tomcat/6.0.16 vulnerabilities. The lowest hanging fruit was Directory Traversal, [CVE-2008-2938](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2008-2938). It was stated in [official Apache Tomcat 6 vulnerabilities page](http://tomcat.apache.org/security-6.html) that the root cause of this vulnerability is JVM, not Tomcat itself. But whatever, it seems simple enough, worth a shot.

This entry on Security Tracker: [Tomcat UTF-8 'AllowLinking' Java Bug Lets Remote Users Traverse the Directory](http://securitytracker.com/id/1020665) shows how to exploit this. You just need encoded dots `%c0%ae%c0%ae` which represents `..`. Seems a no brainer.

Ok, so now we know we probably could download files. But we don't know what file we want to pull. Time to learn about Java and Tomcat.

[Deploying Web Applications to Tomcat](http://oreilly.com/java/archive/tomcat.html) from O'Reilly shows us exactly that. It was a post from 2001, but quick check on other search results showed that the deployment hasn't changed much. The most interesting part was one [page 5](http://oreilly.com/pub/a/java/archive/tomcat.html?page=5) where we learned that Java Web App file would be packaged to a single `.war` file and stored in the web app's directory. Wait, does that mean we can just download `.war` file and we would get the complete source code of the app? Really?

I guess there's only one way to find out then.

{% highlight bash %}
  wget 'http://example.com/innocent/%c0%ae%c0%ae/innocent.war'
    
  Output
  --REMOVED--  http://example.com/innocent/%c0%ae%c0%ae/innocent.war
  Connecting to example.com... connected.
  HTTP request sent, awaiting response... 200 OK
  Length: 91599064 (87M)
  Saving to: `innocent.war'

  2% [==>                                                         ] 2,206,947    728K/s
{% endhighlight %}

Nice. Now to extract the package we can use `jar` command.

{% highlight bash %}
  jar xvf innocent.war
{% endhighlight %}

That will extract everything to current directory. While it is nice to see everything is in there, we can't do anything much because all the `.class` file are unreadable to human being. There are many decompilers out there. Back then I used `jad` but it's no longer being maintained. Mirror download available from [http://www.varaneckas.com/jad/](http://www.varaneckas.com/jad/). Today I just downloaded [JD-GUI](http://java.decompiler.free.fr/?q=jdgui), that works too.