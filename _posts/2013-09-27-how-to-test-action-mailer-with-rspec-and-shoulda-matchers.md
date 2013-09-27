---
layout: default
title: How To Test ActionMailer With RSpec And shoulda-matchers
tags : [rails, actionmailer, shoulda, matcher, spec, test]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

Today I upgraded [shoulda-matchers](https://github.com/thoughtbot/shoulda-matchers) gem from version 1.4.2 to 2.4.0. shoulda-matchers is a bunch of one-liners to test Rails functionality and compatible with RSpec. My spec suite broken after the upgrade. This post is a walkthrough about the changes I had to make to fix my spec suite.

In version 1.4.2, I added these lines in `spec/spec_helper.rb`.

{% highlight ruby %}
  require 'shoulda-matchers'
  include Shoulda::Matchers::ActionMailer
{% endhighlight %}

After upgrade, I got this error:

{% highlight ruby %}
  uninitialized constant Shoulda::Matchers::ActionMailer (NameError)
{% endhighlight %}

A quick peek at the source shows the error is right (because your know, error messages could lie to you), ActionMailer matchers not included in shoulda-matchers anymore. I can have the same functionalities in Mail itself. Read more from [Using Mail with Testing or Spec'ing Libraries](http://rdoc.info/github/mikel/mail#Using_Mail_with_Testing_or_Spec_ing_Libraries).

Now, I just need to remove those two lines in my `spec/spec_helper.rb` and replace it with this:

{% highlight ruby %}
  include Mail::Matchers
{% endhighlight %}

The matchers directly copied from shoulda-matchers, nothing else changed in my spec suite.