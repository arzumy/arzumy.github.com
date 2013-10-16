---
layout: default
title: How To Really Customize The "Deprecated" Facebook's sharer.php
tags : [facebook, sharer]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

[Facebook Share button](https://developers.facebook.com/blog/post/2009/10/26/extending-facebook-share/), or commonly known as `sharer.php` is the easiest way to share link on Facebook. It was initially deprecated in favour of the [Like button](https://developers.facebook.com/docs/reference/plugins/like/) while still keeping the documentation up for reference on [https://developers.facebook.com/docs/share/](https://developers.facebook.com/docs/share/). Now it is fully replaced by [Feed Dialog](https://developers.facebook.com/docs/reference/dialogs/feed/). Facebook Share button no longer officially supported by Facebook. But it still functional and used by many all over the Internet.

It is easy to use. You don't need an App ID, and you don't need to include any Facebook dependencies. But because it is not supported and documented, you are bound to be surprised by any changes made by Facebook.

In my case, I want to include `sharer.php` on [Malaysia Twitter Political Index](http://bit.ly/myelection). I want to user to be able to share it on Facebook, and I want to customize the title on daily basis. Previously you can just do this:

    http://www.facebook.com/sharer.php?u=http://bit.ly/myelection&t=Here's my customized title

But when I did that, I noticed Facebook no longer honour `t` parameter.

I could dynamically generate customized `og` tag, but due to Facebook caching mechanism there's almost no point of doing that. A quick search led me to '[How to pass custom parameters to a Facebook-”Share”-button](http://www.therykers.net/?p=37)'. That page listed down 4 undocumented params that can be used to customize the share page. Here's my new link:

    http://www.facebook.com/sharer.php?s=100
    &p[url]=http://bit.ly/myelection
    &p[images][0]=http://election.gv.my/assets/vote.png
    &p[title]=My customized title
    &p[summary]=My customized summary

I assume you can add more image with `p[images][1]` to `p[images][n]`. I didn't test it out.

Let's test it out on this blog.

    # With t paramater only
    <a href="http://www.facebook.com/sharer.php?u=http://ar.zu.my&t=Rock on Arzumy!">Share</a>
  
    # With everything
    <a href="http://www.facebook.com/sharer.php?s=100
    &p[url]=http://ar.zu.my
    &p[images][0]=http://www.gravatar.com/avatar/2f8ec4a9ad7a39534f764d749e001046.png
    &p[title]=Rock on Arzumy!
    &p[summary]=In this blog, Arzumy will teach you how to rock!>Customized Share</a>

<div style="text-align:center">
<a href="http://www.facebook.com/sharer.php?u=http://ar.zu.my&t=Rock on Arzumy!" class="btn btn-primary" target="_blank">Share</a>
<a href="http://www.facebook.com/sharer.php?s=100&p[url]=http://ar.zu.my&p[images][0]=http://www.gravatar.com/avatar/2f8ec4a9ad7a39534f764d749e001046.png&p[title]=Rock on Arzumy!&p[summary]=In this blog, Arzumy will teach you how to rock!" class="btn btn-primary" target="_blank">Customized Share</a>
</div>

Go ahead test it out :)