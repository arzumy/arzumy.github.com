---
layout: default
title: Why Some Rake Tasks Require :environment
tags : [rails, rake]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

If you look at `lib/tasks` directory in your Rails app, you probably will find few `.rake` files. Those files allow you to run custom rake command like `rake name:print:random_stuff`.

If you look at those files, you might find that some of them look like this:

{% highlight ruby %}
  task :print_username => :environment do
    puts User.first.name
  end
{% endhighlight %}

While some:

{% highlight ruby %}
  task :print_random do
    puts 'random random random'
  end
{% endhighlight %}

With `:environment` your task will run will full Rails environment loaded. That's why you can do `User.first.name` if you have a User model. If you do it this way:

{% highlight ruby %}
  task :print_username do
    puts User.first.name
  end
{% endhighlight %}

Then execute the task:

{% highlight bash %}
  rake print_username
  => uninitialized constant User
{% endhighlight %}

I tend to forget to include `:environment` then smack my head when I see the `uninitialized constant` error.

Depending on what you want to do, you should choose to load environment or not, accordingly.