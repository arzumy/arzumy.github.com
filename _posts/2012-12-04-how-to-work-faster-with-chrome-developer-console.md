---
layout: default
title: How To Work Faster With Chrome Developer Console
tags : [chrome, tips, tricks, productivity]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

Web developer spent considerable amount of time working on browser console. You could be working mostly on [Firefox Firebug](https://getfirebug.com/) or [Chrome Developer Console](https://developers.google.com/chrome-developer-tools/docs/console). It's good to learn a trick or two to speed up development.

I learned this from [Egor Homakov](http://homakov.blogspot.com/). We were smoking shisha when he decided to pull out his machine and get some work done. And while he was at it, he showed us few tricks.

The first one was `$0`. The command will return the most recently selected node to console. You just have to go to `Elements`, click on a node and type `$0` in console. You can also type `$1` to return the previously selected element. Then `$2` and so on.

If you've been a good developer and bothered to check [https://developers.google.com/chrome-developer-tools/docs/console](https://developers.google.com/chrome-developer-tools/docs/console) then you've probably already noticed this. I have never been to that page until today.

But, he did show something else too. `$_` to get the last returned value in console. Similar to `_` in `irb`.

{% highlight javascript %}
  > 2 + 2
  => 4
  > var b = $_
  => undefined
  > b
  => 4
{% endhighlight %}

And that one wasn't documented in [https://developers.google.com/chrome-developer-tools/docs/console](https://developers.google.com/chrome-developer-tools/docs/console) yet. Nor supported by [Firebug's Command Line API](http://getfirebug.com/wiki/index.php/Command_Line_API) too.