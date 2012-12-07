---
layout: default
title: How To Make Sure Your Free Wordpress Theme Is Safe From Virus
tags : [virus, php, wordpress, scanner]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

I got to help a friend cleaning up his [Wordpress](http://wordpress.org/) installation from viruses today. Hosting provider emailed and pointed us to the exact files with virus. I'm using virus term loosely, they were actually [PHP backdoor shells](http://en.wikipedia.org/wiki/Backdoor_Shell).

I don't use Wordpress that much. I think I have few installations here and there, mostly left forgotten. So today I got to learn about basic safety when dealing with Wordpress.

Keep your Wordpress up-to-date. There's almost no reason why you won't or can't upgrade. Updating Wordpress installation is so easy now, just a single click.

Majority of free Wordpress themes are infected with some sort of backdoor, or maybe just simple affiliate links. Clean free themes are hard to come by. If you want free theme, get them from trusted source. I would trust themes in Wordpress.org itself. Just search and install within Wordpress. I don't know any other free themes sites that I could trust.

If you really have to use free theme, then you have to take a little precaution. One thing that we did is to install [Theme Authenticity Checker (TAC)](http://wordpress.org/extend/plugins/tac/) plugin. It would scan for obfuscated code. There's no reason why theme creators want to obfuscate the code unless they are hiding something.

You could also scan the compressed theme file after downloading. Maybe that would help. I don't know because I'm using OSX, and OSX users are not known for using antivirus software. But we have terminal though. We could do this: 

{% highlight bash %}
  unzip -d freetheme freetheme.zip
  cd freetheme
  grep -Rn "base64_decode *(" .
  => ./footer.php:1:<? eval(gzinflate(str_rot13(base64_decode('FZfHDoTYFVF/xTuPxYKccXtTx...
{% endhighlight %}

From the grep result we could see there's a base64 encoded string. Not a good sign. If you just want to be safe, just delete and don't use it. 

To cover more bases, I found this command from [The Official ROOTCON Blog](http://blog.rootcon.org/2012/04/simple-kung-fu-grep-for-finding-common.html).

{% highlight bash %}
  grep -RPn "(passthru|shell_exec|system|phpinfo|base64_decode|chmod|mkdir|fopen|fclose|readfile|php_uname|eval|tcpflood|udpflood|edoced_46esab) *\(" .
{% endhighlight %}

