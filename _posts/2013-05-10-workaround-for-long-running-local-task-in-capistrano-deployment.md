---
layout: default
title: Workaround For Long Running Local Task In Capistrano Deployment
tags : [capistrano, deploy, rails]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

All our assets in [SAYS.com](http://says.com) are hosted in [Amazon S3](http://aws.amazon.com/s3/) and served by [Amazon CloudFront](http://aws.amazon.com/cloudfront/). The outbound traffic for these files are too high, we ended up hitting our servers' bandwidth limit sooner that expected. By off loading the files to Amazon, bandwidth no longer a candidate to performance bottleneck.

Our [Capistrano](http://capistranorb.com/) deployment now includes new tasks: compiles assets locally, syncs to S3 bucket and uploads `manifest.yml` to all our servers. It works fine most of times, except once in while we'll get random `IOError` after assets compiling. Then that 'once in a while' error becomes 'often', then it becomes 'most of the time'. Something's wrong, definitely. 

We later determined that the error was caused by SSH connections to the servers dropped due to long idle time while waiting for local assets precompile. We toyed around SSH configurations i.e. increase idle time, time to live etc. But those are just guess works because we can't tell for sure how long assets precompile will take. Of course for now we can average out the compiling time, but in the future it might take longer.

What we are doing now is to kill those connections after assets precompile, and let capistrano creates new connections. We just include this line after local assets precompile:

{% highlight ruby %}
	teardown_connections_to(sessions.keys)
{% endhighlight %}

Works like charm! One might argue that the deployment will be a bit slower because we need to reestablish SSH connection, we are completely fine with it. The benefit is better than the downside.