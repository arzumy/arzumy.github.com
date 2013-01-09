---
layout: default
title: How To Show Highchart's Tooltip On Load
tags : [javascript, highchart, tooltip]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

In [Malaysia Twitter Political Index](http://election.gv.my), I didn't show legend for the chart because it is ugly. And I don't feel like I want to make an effort to style it. Plus, when you hover on the chart, it will show tooltip with details. So I figured, there's no need for legend.

But, not everyone will hover. They will get confused. I looked at [Twitter Political Index](https://election.twitter.com/), no legend too, instead it just shows the tooltip, I decided to do the same.

It's not hard to figure out, all I had to do was google for it :P I found my answer in this [forum](http://ftp.highslide.com/forum/viewtopic.php?f=12&t=18889) (or you can [fiddle it here](http://jsfiddle.net/L2TFd/28/)).

I just need to loop the `series` to get to the last point and refresh tooltip with the points.

{% highlight javascript %}
chart = new Highcharts.Chart({
  chart: {...},
  credits: {...},
  title: {...},
  xAxis: {...},
  yAxis: {...},
  tooltip: {...},
  legend: {...},
  plotOptions: {...},
  series:{...}}, function(chart){
    // Last point in graph...
    var points=[];
    if(chart)
    {
      for(var i=0;i<chart.series.length;i++)
      points.push(chart.series[i].points[chart.series[i].points.length-1]);
      chart.tooltip.refresh(points);
    }
});
{% endhighlight %}