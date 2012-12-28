---
layout: default
title: How To Add Footnote In Markdown
tags : [markdown, syntax]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

I wrote my posts in [Markdown](http://daringfireball.net/projects/markdown/). You can pull my blog from [https://github.com/arzumy/arzumy.github.com](https://github.com/arzumy/arzumy.github.com) and have a look.

I used footnote in these two posts:

* [How To Quickly Search Your Terminal History](/how-to-quickly-search-your-terminal-history/) ([view source](https://raw.github.com/arzumy/arzumy.github.com/master/_posts/2011-01-01-how-to-quickly-search-your-terminal-history.md))
* [My First Phreaking Lesson](/my-first-phreaking-lesson/) ([view source](https://raw.github.com/arzumy/arzumy.github.com/master/_posts/1990-01-01-my-first-phreaking-lesson.md))

This is a footnote[^fn-sample]. Go ahead click it.

Here's how we did that:

    This is a footnote[^fn-sample] 
    [^fn-sample]: Now I gotta cut loose. Footloose, kick off the Sunday shoes. Click this to go back up >>

All you need is to have `[^anchor_name_here]` for the link and `[^anchor_name_here]:` for the footnote. The key is to start with `[^]`. Ideally since it's a footnote, you should place `[^anchor_name_here]:` on the bottom. But hey, entirely up to you! With this syntax, you'll get `<sup></sup>` tag for free with link to the anchor and running numbers for all footnotes on the same page.

I intentionally left a blank space here so you can test the link and actually move from the number to footnote back and forth.

<span style="display:block; height:1200px;">&nbsp;</span> 



































































[^fn-sample]: Now I gotta cut loose. Footloose, kick off the Sunday shoes. Click this to go back up >>