---
layout: default
title: How To Quickly Change All Your Remote Git Repos URL
tags : [productivity, bash, git]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

At [SAYS](http://says.com) we hosted all our repo in [GitHub](http://github.com). It must have been 4 5 years now. We've changed our domain few times now, to better reflect our company. But we never bothered to change our organization name in GitHub. So until today it is in [https://github.com/youthasia] (https://github.com/youthasia).

Today I decided to update our details in GitHub and while I am at it, change the name to SAYS. So I did just that knowing that we can't sync our local repos until we change the URL in our local machine.

Because we have quite a lot of repos, changing them one by one didn't make much sense. This is what I did.

I have a directory called `workspace` where I store all the repos.

    - workspace
      \_ repo_one
      \_ repo_two
      \_ repo_three
      ....
      
I want to look for `.git/config` and change the URL in these lines:

    [remote "origin"]
    	fetch = +refs/heads/*:refs/remotes/origin/*
    	url = git@github.com:youthasia/example.git # Change youthasia to says
    	
I did this:

{% highlight bash %}
  cd workspace
  for i in `find . -name config` ; do sed -i.back s/git@github\.com:youthasia/git@github\.com:says/g $i; done;
{% endhighlight %}

What I did was to change to `workspace` directory, find all `config`, `sed` will create backup file, change *youthasia* to *says* and save it. Now we can sync back with GitHub.