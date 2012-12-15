---
layout: default
title: Simple Array In YAML
tags : [yaml, yml, ruby, array]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

I'm so used to load `.yml` file to hash that I've forgotten `.yml` can just load directly to array. In fact, that's like the most basic way of doing this.

This is how I normally create my `.yml`.

{% highlight yaml %}
  # test.yml
  
  sample:
    first_test: one
    second_test: two
{% endhighlight %}

That will give me a hash.

{% highlight ruby %}
  require 'yaml'
  Yaml.load_file('test.yml')
  => {"sample"=>{"first_test"=>"one", "second_test"=>"two"}}
{% endhighlight %}

In my mind, that's the default way of doing things. Few days back I decided to include all my previous talks and interviews in my site. You can see how I started in this [commit](https://github.com/arzumy/arzumy.github.com/commit/b3f35c92b8c4c7e72403c26db44a6a560d0957f8). Take a close look at my `index.html` file.

{% highlight html %}
  <li>
    <span class="label label-info">12 Jan 2012</span> <a href="http://bfm.my/lifehacks.html">BFM 89.9: The Business Station - Lifehacks</a>
  </li>
  <li>
    <span class="label label-info">28 Sep 2011</span> <a href="http://www.bfm.my/techtalk_hackweekday2011.html">BFM 89.9: The Business Station - HackWEEKDAY 2011</a>
  </li>
  <li>
    <span class="label label-info">01 Sep 2005</span> <a href="http://www.nib.com.my/archives/text/view/9426915?pos=3&hide_header=1&resultset=nstpec%3Awww/cross-search/search.php%3A_1355414435%3Aresultset">New Straits Times: Computimes - Hacking competition winners</a>
  </li>
{% endhighlight %}

While this worked, I started to see pattern emerged. I have the same list for talks as well. I'll have to repeat the same `li` over and over again. Maybe it would be better if I could store my interviews and talks in a collection and just loop it here instead. Configuration for this site stored in `_config.yml`, so seems like it would make sense if I store my interviews and talks there too. 

My problem was, how do I store them in an array? I checkout [yaml.org](http://www.yaml.org) and the first example for ruby is [simple sequence](http://www.yaml.org/YAML_for_ruby.html#simple_sequence) list. List without key. That works really well. Example:

{% highlight yaml %}
  # test.yml
  
  sample:
    - one
    - two
{% endhighlight %}

That will give me hash with `sample` as the key, and an array of `one, two`.

{% highlight ruby %}
  require 'yaml'
  Yaml.load_file('test.yml')
  => {"sample"=>["one", "two"]}
{% endhighlight %}

With that knowledge, I made this [commit](https://github.com/arzumy/arzumy.github.com/commit/57860adef11092846386c9e54210f6569566ed9a) and now I can use for loop in my `index.html` for talks and interviews.

{% highlight html %}
  {{ "{% for interview in site.content.interviews " }}%}
    <li>
      <span class="label label-info">{{ "{{ interview.date " }}}}</span> <a href="{{ "{{ interview.url " }}}}">{{ "{{ interview.title " }}}}</a>
    </li>
  {{ "{% endfor " }}%}  
{% endhighlight %}

Much better!