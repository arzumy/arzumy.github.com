---
layout: default
title: How To Quickly Search Your Terminal History
tags : [terminal, bash, productivity, tips]
---
<p><small>Early 2011</small></p>

One of benefit of pair programming is you got to learn things that you don't even know you need to learn. When I paired with [Mike Mazur](http://mmazur.com/), one of the [Pivotal Labs](http://pivotallabs.com/)[^fn-neo] engineer, I got to learn a very useful trick. 

Normally when I want to refer back to commands that I've used before, I'd do this:

{% highlight bash %}
  history | grep previous_command
{% endhighlight %}

Then I'd cut and paste the result to execute. Not ideal, but it worked for me. Because I didn't know better. 

Until he showed me the right way of doing it. Comes reverse-i-search. All you need to do is press `ctrl + r` in terminal and start typing the command you want to search for. For example, I want to search for `server`:

{% highlight bash %}
  ctrl + r
  serv 
  => (reverse-i-search)`serv': jekyll --server --auto
{% endhighlight %}

If that's not the command you looking for, just keep tapping `ctrl + r` until you find the right one. Then just press `enter` to execute. Handy!

Also, it would be better if you increase your history size. Just add these lines to your profile file, one of those `.bashrc`, `.bash_profile` or `.profile`.

{% highlight bash %}
  HISTFILESIZE=1000000000
  HISTSIZE=1000000
{% endhighlight %}

The size is up to you, I got those values from [lifehacker](http://lifehacker.com/278888/ctrl%252Br-to-search-and-other-terminal-history-tricks).

[^fn-neo]: Now called [Neo](http://neo.com)