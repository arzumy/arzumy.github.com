---
layout: default
title: How To Steal Twitter Password
tags : [hack, phishing, mobile, twitter]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

Difficulty level maybe a bit high, but it's doable.

Recently I've been using [Lift](http://lift.do/) to start new habit. I like it because it is not annoying compared to other apps out there. It just passively waits there tracking your habit. 

After using it for few days, I figured I'd like to connect my [Twitter account](https://twitter.com/arzumy). There's nothing much you could achieve by doing that, it will pull your avatar and that's about it. I can't justify why would I want to connect my Twitter account. But I went ahead and clicked *Connect to Twitter* anyway.

I got redirected to this screen:

<div class="posts-images">
  <img src="/assets/images/posts/2012-12-16-lift-screenshot.png" title="Lift Twitter authorize screen" class="img-polaroid" style="max-width: 400px;"/>
</div>

What's wrong with this page? There's no way I can verify that the page I'm seeing is a real Twitter auth page. It could be a phishing page made to steal my login credentials.

Since this page is loaded in a web wrapper, you can create a fake Twitter auth page and store the login credentials when submitted. Then, redirect them back again to the real Twitter page. Unsuspecting victim will just try to login again. Because no one can see the URL, they won't know. 

Maybe it's a bit hard to pull on legitimate app distributed through Apple App Store. But if you are distributing the app through other unofficial channels, you can pull this of quite easily.

NOTE: Lift allows you to connect your Twitter account from web, [http://lift.do/login](http://lift.do/login). That's a better option and signs that you can trust the app. I'm not implying that Lift is stealing your password. I'm using it as example because I stumbled upon it.