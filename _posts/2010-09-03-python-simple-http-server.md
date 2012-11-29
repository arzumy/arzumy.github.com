---
layout: default
title: Python Simple HTTP Server
tags : [python, web, server, tips]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

Every now and then I need to serve something over HTTP. Most of the times, it was to test out JavaScript with callbacks. It won't run simply by double clicking the sample HTML file, you need to serve everything. 

With python simple HTTP server, all I need to do is just drop in the directory and run:

{% highlight bash %}
  python -m SimpleHTTPServer

  Output:
  Serving HTTP on 0.0.0.0 port 8000 ...
{% endhighlight %}

It will start HTTP server on default port 8000. To use other port, just add the port number:

{% highlight bash %}
  python -m SimpleHTTPServer 8001

  Output:
  Serving HTTP on 0.0.0.0 port 8001 ...
{% endhighlight %}

Head up to `http://localhost:8001` to view it. Simple, easy, straight to the point.