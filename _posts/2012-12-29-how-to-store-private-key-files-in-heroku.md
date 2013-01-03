---
layout: default
title: How To Store Private Key Files In Heroku
tags : [heroku, config, cert]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

In my latest project, [Malaysia Twitter Political Index](http://election.gv.my/), I used [Google Prediction API](https://developers.google.com/prediction/) and hosted it on [Heroku](http://heroku.com). To use Google API, you have to create Client ID to authenticate with the endpoints. Since I'm calling the API directly from the server, not end user, I have to create [Service Accounts](https://developers.google.com/console/help/#service_accounts) client ID.

Google generated a key pair for me. I then downloaded the private key and I would use it in my app. The key is formatted with [PKCS #12](http://www.rsa.com/rsalabs/node.asp?id=2138).

I used [google-api-ruby-client](https://github.com/google/google-api-ruby-client) gem, here's the example to authenticate:

{% highlight ruby %}
  require 'google/api_client'
  client = Google::APIClient.new
  key = Google::APIClient::PKCS12.load_key('/path/to/key.p12', 'notasecret') # this is where you load the key
  service_account = Google::APIClient::JWTAsserter.new(
      '123456-abcdef@developer.gserviceaccount.com',
      'https://www.googleapis.com/auth/prediction',
      key)
  client.authorization = service_account.authorize
{% endhighlight %}

That worked well in development environment. To make it work in Heroku deployment, I had few options:

1. Include the private key in my git repository.
1. Store it in S3, and pull it when needed.
1. Use custom build-pack which will include the key.
1. Store it as config vars.

Option 1 is the easiest. But maybe not ideal. While it's true that I'm the only one working on the project, I wouldn't know if others might join in any time in the future.

Option 2 and 3 seems like a lot of unnecessary moving pieces involves. 

Option 4 is ideal in this scenario. I'm already storing all sensitive information in config vars anyway. Now how would I do that?

I know config vars are string. So I took a peek at the `.p12` file.

{% highlight bash %}
  cat example.p12
  => ??0?0?c0?(??   *?H??
  *?H??

  ???0??0(
  ...
{% endhighlight %}

Bunch of jumbled up stuff. Ok, but PKCS #12 is just a format, maybe I can store the key in different format? I took a look back at the code and inspected what returned by `Google::APIClient::PKCS12.load_key`.

{% highlight ruby %}
  require 'google/api_client'
  key = Google::APIClient::PKCS12.load_key('/path/to/key.p12', 'notasecret') 
  => -----BEGIN RSA PRIVATE KEY-----
  MMMMMMMMaaaaaammmmammamamammamaasdhkghkdahgj8234joihsdfJHHKJGHGG
  ...
  -----END RSA PRIVATE KEY-----
  
  key.class
  => OpenSSL::PKey::RSA
{% endhighlight %}

Alright, that's something that I recognized. It was `OpenSSL::PKey::RSA` and returned a string. Let's try load it directly instead of using `Google::APIClient::PKCS12.load_key`.

{% highlight ruby %}
  require 'google/api_client'
  client = Google::APIClient.new
  private_key = "-----BEGIN RSA PRIVATE KEY-----
  MMMMMMMMaaaaaammmmammamamammamaasdhkghkdahgj8234joihsdfJHHKJGHGG
  ...
  -----END RSA PRIVATE KEY-----"
  key = OpenSSL::PKey::RSA.new private_key_, 'notasecret'
  service_account = Google::APIClient::JWTAsserter.new(
      '123456-abcdef@developer.gserviceaccount.com',
      'https://www.googleapis.com/auth/prediction',
      key)
  client.authorization = service_account.authorize
{% endhighlight %}

And we are good! Now instead of storing `.p12` file, I'd store that instead. Heroku config vars support multiline string. I just had to double quote it.

{% highlight bash %}
  heroku config:add PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----
  MMMMMMMMaaaaaammmmammamamammamaasdhkghkdahgj8234joihsdfJHHKJGHGG
  ...
  -----END RSA PRIVATE KEY-----"
{% endhighlight %}

I make sure I passed the config var when I load the key.

{% highlight ruby %}
  key = OpenSSL::PKey::RSA.new ENV["PRIVATE_KEY"], 'notasecret'
{% endhighlight %}

That's how you store private key in Heroku.