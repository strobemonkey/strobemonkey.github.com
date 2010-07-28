---
layout: blog
title: "Ruby on Rails, YouTubeG, gem config problem"
summary: Adding YouTubeG gem to environment.rb needs a lib attribute
---

After adding this to environment.rb:

{% highlight bash %}
config.gem 'youtube-g', :version => '0.5.0' 
{% endhighlight %}

running script/server gave this:

{% highlight bash %}
Missing these required gems:
  youtube-g  = 0.5.0
{% endhighlight %}

which is corrected by adding the lib attribute, thus:

{% highlight bash %}
config.gem 'youtube-g', :lib => 'youtube_g', :version => '0.5.0' 
{% endhighlight %}
