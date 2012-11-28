---
layout: default
title: Shared Session Cookies Gotchas
tags : [rails, sessions, authentication, cookies]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

*Originally posted in [Ruby on Rails - Malaysia (English)](https://www.facebook.com/groups/rails.my.english/doc/10150213012561848/) group.*


Today I made a mistake. I pushed a line of code that effectively made the users can neither login nor logout. So I thought I'd shared the problem and solution with you guys

**Intention**

We want the session cookie to be shared within the subdomain. For example, forum.domain.com could access session cookie from www.domain.com. This allows seamless integration between multiple apps.

**How-to**

It's easy to achieve that. You'll just have to modify *config/initializers/session_store.rb* and add *:domain* option. Like so,

    MyApp::Application.config.session_store :cookie_store, :key => '_my_app_session', :domain => ".domain.com"

That works, but it's not so cool since you'll have to hardcode domain name. Better solution would be to use *:all* value

    MyApp::Application.config.session_store :cookie_store, :key => '_my_app_session', :domain => :all

That will make session cookie's host to be .domain.com or if your domain is google.com, it'll be .google.com. Cookie's host will follow what ever you current domain.

**Problem**

They are two major problems when you deploy the code. First, current users now locked to their current session state. If they are logged in, they can't logout and vice versa. This happens because we now have two session cookies with same name (_my_app_session) but with different host. And when you have that situation, cookie with matching host will take precedence. Hence, we are reading the old cookie (the one with host www.domain.com) which is wrong, so our session is messed up.

User could fix this by clearing up the browser's cookies. But that's totally uncool. Also, this won't affect new user, so that, on the other hand, is cool.

Second problem is, when working on development environment, visiting http://localhost:3000 won't work. Because we are expecting the cookie host to be .localhost but it's always saved as localhost. It will always be invalid.

**Solution**

The solution is obvious, you just need to delete the old cookie and just read the new one. But somehow the implementation wasn't that obvious to me at first. My first option was to add a line of code that will delete the old cookie. It works, but it's bad because it'll be a stale code after a while. That line will never be used once all old cookies delete, and never used on new users.

I've searched high and low for better solution when it suddenly hits me. I don't need to delete the old cookie, I just need to make sure the app won't read it. Just invalidate the old cookie. Since I have a conflicted name, why don't just change the name? So I did, and it works like a charm. My new *session_store.rb* looks like this

    MyApp::Application.config.session_store :cookie_store, :key => '_new_my_app_session', :domain => :all

For second problem, a quick workaround is to not use localhost. Use alias like app.local, local.my etc. Or just make sure shared domain not used in development mode, like so:

    MyApp::Application.config.session_store :cookie_store, :key => '_new_my_app_session', :domain => Rails.env.development? ? nil : :all

That's about it. Now the app runs smoothly for old and new users.

Cheers!