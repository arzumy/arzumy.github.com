---
layout: default
title: heroku_san Deploy Strategies And Bundle Blunder
tags : [heroku, deployment, rails, ruby, bundler]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

What's annoying about [heroku_san](https://github.com/fastestforward/heroku_san) is it will run migration all the time. Not cool. Most of the times I don't have any migration to run. Few seconds wasted waiting for the environment to load yet no migration needed.

Good thing heroku_san allows custom deploy strategy. [Here's the complete tutorial](https://github.com/fastestforward/heroku_san/wiki/Deploy-Strategies). Here's my short version. All I want is for the deployment to just push the commit and that's it. No need for migration and restart. So I just cut as paste from the tutorial to */lib/tasks/heroku.rake*

{% highlight ruby %}
  require 'heroku_san'
  
  class MyStrategy < HerokuSan::Deploy::Base
    def deploy
      super
    end
  end
  
  HerokuSan.project = HerokuSan::Project.new(Rails.root.join("config","heroku.yml"), :deploy => MyStrategy)
  ...
{% endhighlight %}
    
Come to think of if, I might as well just do this instead:

{% highlight ruby %}
  require 'heroku_san'
  
  HerokuSan.project = HerokuSan::Project.new(Rails.root.join("config","heroku.yml"), :deploy => HerokuSan::Deploy::Base)
  ...
{% endhighlight %}
    
Since I'm not adding anything to it. That's it. Now deploying to heroku should not invoke rake db:migrate.

Except, it didn't work. The gotcha? Checkout my Gemfile

{% highlight ruby %}
  group :development do
    gem 'heroku_san'
    ...
  end
{% endhighlight %}
    
Yeah, no heroku_san on production. I then just add simple check.

{% highlight ruby %}
  if Rails.env.development?
    require 'heroku_san'
    
    class MyStrategy < HerokuSan::Deploy::Base
      def deploy
        super
      end
    end
  
    HerokuSan.project = HerokuSan::Project.new(Rails.root.join("config","heroku.yml"), :deploy => MyStrategy)
  end
{% endhighlight %}
    
That should do it. Now we can deploy.