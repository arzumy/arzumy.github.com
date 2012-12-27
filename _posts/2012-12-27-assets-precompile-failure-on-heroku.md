---
layout: default
title: assets:precompile Failure On Heroku
tags : [heroku, deploy, assets]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

This issue always caught me off guard when deploying new app on [Heroku](http://heroku.com). The root cause is simple, `assets:precompile` requires the app's environment to be loaded. But because it runs during slug compilation, no config vars available to the app yet. That's why you'll get error like this:

    could not connect to server: Connection refused
    Is the server running on host "127.0.0.1" and accepting
    TCP/IP connections on port xxxx?
    
App trying to connect to database but `DATABASE_URL` var not correctly set yet. 

The simplest solution is to tell the app to not initialize when we are compiling the assets. Add this line:

{% highlight ruby %}
  # config/application.rb

  config.assets.initialize_on_precompile = false
{% endhighlight %}

That should do it.

Back then we were able to use `user-env-compile` feature from [Heroku Labs](https://devcenter.heroku.com/categories/labs) but it is no longer available. `user-env-compile` exposes your config vars during slug compilation. You can read about it here [https://devcenter.heroku.com/articles/labs-user-env-compile](https://devcenter.heroku.com/articles/labs-user-env-compile).