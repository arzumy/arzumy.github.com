---
layout: default
title: jekyll And liquid Loops
tags : [jekyll, liquid, syntax]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

I've been using [jekyll](https://github.com/mojombo/jekyll) to run this site since 3 years ago. But I didn't do much with it. It was just a single page site. Surprisingly enough, this site is a PageRank 3 site! Now who said content is king? :P

Now that I'm starting to convert this site to a blog, I've been using jekyll as it's intended to be. I got to learn a bit more about it. Specifically the syntaxes for [liquid](https://github.com/Shopify/liquid), template engine used in jekyll.

Since this is a blog, having [atom.xml](/atom.xml) for feed is not a bad idea. I just copied (yes, this pattern starting to emerge now) from [Tom Preston-Werner's](https://github.com/mojombo/mojombo.github.com/blob/master/atom.xml) blog and edited it a bit.

{% highlight xml %}
  <?xml version="1.0" encoding="utf-8"?>
  <feed xmlns="http://www.w3.org/2005/Atom">
  
   <title>{{ "{{ site.title " }}}} - {{ "{{ site.tagline " }}}}</title>
   <link href="{{ "{{ site.production_url " }}}}/atom.xml" rel="self"/>
   <link href="{{ "{{ site.production_url " }}}}"/>
   <updated>{{ "{{ site.time | date_to_xmlschema " }}}}</updated>
   <id>{{ "{{ site.production_url " }}}}</id>
   <author>
     <name>{{ "{{ site.author.name " }}}}</name>
     <email>{{ "{{ site.author.email " }}}}</email>
   </author>
  
   {{ "{% for post in site.posts " }}%}
   <entry>
     <title>{{ post.title }}</title>
     <link href="{{ "{{ site.production_url " }}}}{{ "{{ post.url " }}}}"/>
     <updated>{{ "{{ post.date | date_to_xmlschema " }}}}</updated>
     <id>{{ "{{ site.production_url " }}}}{{ "{{ post.id " }}}}</id>
     <content type="html">{{ "{{ post.content | xml_escape " }}}}</content>
   </entry>
   {{ "{% endfor " }}%}
  
  </feed>
{% endhighlight %}

But if you look at the loop

    {{ "{% for post in site.posts " }}%}

That will list down all posts since the dawn of time. I don't have a problem with it yet. At this time of writing, I only have 4 posts. This could be a problem if I'm persistent enough to churn out new post every few days. I decided not to be pragmatic and solve this non-problem now. 

A quick look here [https://github.com/Shopify/liquid/wiki/Liquid-for-Designers](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers) shows that I can just add *limit* to the loop.
  
    {{ "{% for post in site.posts limit:10 " }}%}

There you go. Non-problem solved.

NOTE: If you want to use *code* tag to display liquid syntax like I did above, then you'll have to temporarily disable tag processing. You can use *{{ "{% raw " }}%}* tag.

    {{ "{% raw " }}%}
      {{ "{% for post in site.posts limit:10 " }}%}
    {{ "{% endraw " }}%}
    
UPDATED: [Github Pages](http://pages.github.com/) won't run *{{ "{% raw " }}%}*  plugin. So you can escape it with this instead 

    {{ "{{ " }}"{{ "{% for post in site.posts limit:10" }} " }}{{ "}}%}
    
Credit goes to this [Stack Overflow answer](http://stackoverflow.com/questions/10154690/github-pages-isnt-applying-my-template).