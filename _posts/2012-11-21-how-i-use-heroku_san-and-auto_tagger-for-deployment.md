---
layout: default
title: How I use heroku_san with auto_tagger for deployment
tags : [heroku, deployment, rails, ruby]
---

In [SAYS](http://says.com), we use [capistrano-gitflow](https://github.com/technicalpickles/capistrano-gitflow) in our deployment recipe. It provides us with tagging and logging workflow. Made our life easier.

Currently I'm working on one of our client's project. It require us to deploy on [heroku](http://heroku.com). I would love to use the same deployment workflow that we use in SAYS. My initial plan was to write simple rake task to tag and push to heroku. Of course, I googled it up first, see how others approached deployment on heroku.

Most solutions that I found are using production/staging branch. Which is not what I wanted. That's when I stumbled upon heroku_san. I remember reading about it but didn't bother to test it out. [There's a good tutorial here](https://github.com/fastestforward/heroku_san/wiki/Using-Autotagger). But I don't use CI for this project, so I simplify it a bit.

I added tag to heroku.yml for production stage, staging remains the same.

    production:
	    tag: staging/*
	    ...
		
Then I just paste from the example to heroku.rake, I removed ci from STAGES.

  	STAGES = %w[staging production]

  	def create_and_push(stage)
  	  auto_tag = AutoTagger::Base.new(stages: STAGES, stage: stage, verbose: true, push_refs: false, refs_to_keep: 100)
  	  tag = auto_tag.create_ref(auto_tag.last_ref_from_previous_stage.try(:sha))
  	  sh "git push origin #{tag.name}"
  	  auto_tag.delete_locally
  	  auto_tag.delete_on_remote
  	end

  	task :before_deploy do
  	  sh "git fetch --tags"
  	end

  	task :after_deploy do
  	  each_heroku_app do |stage|
  	    create_and_push(stage.name)
  	  end
  	end

  	namespace :autotag do
  	  desc "Create an autotag for stage, default: #{STAGES.first}"
  	  task :create, :stage do |t, args|
  	    create_and_push(args[:stage] || STAGES.first)
  	  end
  	end

That's it. *rake staging deploy* will deploy to staging, then tag it with *staging/datetime*. *rake production deploy* will deploy anything tagged as *staging/\**.