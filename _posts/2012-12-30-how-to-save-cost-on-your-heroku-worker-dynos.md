---
layout: default
title: How To Save Cost On Your Heroku Worker Dynos
tags : [heroku, dynos]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

On [Malaysia Twitter Political Index](http://election.gv.my/) project, I'm using [delayed job](https://github.com/collectiveidea/delayed_job) extensively. I used it to pull tweets, query sentiments and update sentiments. These jobs created every 10 minutes by [Heroku Scheduler](https://addons.heroku.com/scheduler), Heroku's answer to traditional cron job.

These jobs took about 2 or 3 minutes to finish. While waiting for the next cron job, worker dyno are idle for 7 or 8 minutes. I don't like it because I have to pay for it!

I'm in luck because Heroku provides a [ruby client gem](https://github.com/heroku/heroku.rb) to interact with [Heroku API](https://api-docs.heroku.com/). With this gem, we can easily start or stop dynos from within the app itself. There are few ways we can leverage on this, I chose to create rake task that will spin the dyno up and down. Then let Heroku Scheduler run it in specific intervals.

{% highlight ruby %}
  # lib/tasks/heroku.rake
  
  namespace :dynos do
    desc 'up worker'
    task up: :environment do
      unless Delayed::Job.count.zero?
        heroku = Heroku::API.new(:api_key => ENV['API_KEY'])
        heroku.post_ps_scale(ENV['APP_NAME'], 'worker', '1')
      end
    end

    desc 'down worker'
    task down: :environment do
      if Delayed::Job.where(last_error: nil).count.zero?
        heroku = Heroku::API.new(:api_key => ENV['API_KEY'])
        heroku.post_ps_scale(ENV['APP_NAME'], 'worker', '0')
      end
    end
  end
{% endhighlight %}

I scheduled `rake dynos:up` to run immediately after all those sentiments job created, and scheduled `rake dynos:down` 3 minutes after that. Dyno will only spin up if there's job, and will spin down if there's no more pending jobs. Of course if you really want to push it, you can schedule it to run every minute just be sure.

If you look at `heroku.post_ps_scale(ENV['APP_NAME'], 'worker', '0')`, you can see that I assigned my app name to `APP_NAME` config var. I did that because I can't find a way to programmatically get my app name from Heroku. If you use [New Relic addon](https://addons.heroku.com/newrelic) you could get it from `NEW_RELIC_APP_NAME`, but I feel more comfortable setting it myself.

Now I managed to save around 70% of my estimated monthly dyno cost. If you want to go gung ho with it, potentially you could add this to `Delayed::Job`'s `after_create` and `after_delete` callbacks. That really depends on the nature of your jobs though.

NOTE: I wrote about '[How I Use Two Dynos On heroku For Free](/use-two-dynos-on-heroku-for-free/)' but I like Heroku API way better than using separate app to run worker. Less moving pieces to maintain.