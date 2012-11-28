---
layout: default
title: Aspen Shutdown, Serve Static HTML From Cedar And run:detached
tags : [heroku, php, cedar, aspen]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

[Heroku](http://heroku.com) is shutting down the Aspen stack effective November 26, 2012. I have few apps still running on Aspen, but most of them are trial apps, so I never bothered to migrate to Cedar. Except, today I realized [Codelovr's site](http://codelovr.com) was down. It was still on Aspen!

I quickly pulled down the code. After quick check, it was a rails app during the early days. But it was serving just a single html file with one stylesheet. There's no reason why we should continue with that. I decided to just serve static html directly. A quick search showed there are multiple ways to serve static html on Cedar stack. I chose to use Apache to serve it. I got the tutorial from [http://kennethreitz.com/static-sites-on-heroku-cedar.html](http://kennethreitz.com/static-sites-on-heroku-cedar.html)

First we must make sure `index.php` exists. I copied my `index.html` to `index.php`. I've copied my html and stylesheet to new directory.

{% highlight bash %}
  cp index.{html,php}
{% endhighlight %}
    
To understand why we need `index.php`, we can have a look at how heroku-buildpack-php detect it. The code is here [https://github.com/heroku/heroku-buildpack-php/blob/master/bin/detect](https://github.com/heroku/heroku-buildpack-php/blob/master/bin/detect). The exact line is

{% highlight bash %}
  if [ -f $1/index.php ]; then
{% endhighlight %}
    
Then we turn off php engine

{% highlight bash %}
  echo 'php_flag engine off' > .htaccess
{% endhighlight %}
    
Commit everything, create new Cedar stack and push!

{% highlight bash %}
  git init . 
  git add .
  git commit -am 'initial commit'
  heroku apps:create 
  git push heroku
  heroku open
{% endhighlight %}
    
If everything is cool we can delete the Aspen app and add back our custom domain to the new Cedar app.

Another thing I learned today is how to run task in background. There are places where port 5000 is blocked and running `heroku run rake db:migrate` always ended up with time out connecting to process. To bypass that, we just run it in background with `run:detached`

{% highlight bash %}
  heroku run:detached rake db:migrate
  
  Output:
  Running `rake db:migrate` detached... up, run.4271
  Use `heroku logs -p run.4271` to view the output.
  
  heroku logs -p run.4271 # To view log
{% endhighlight %}
    
You can read more here [https://devcenter.heroku.com/articles/oneoff-admin-ps#running-tasks-in-background](https://devcenter.heroku.com/articles/oneoff-admin-ps#running-tasks-in-background)
    
