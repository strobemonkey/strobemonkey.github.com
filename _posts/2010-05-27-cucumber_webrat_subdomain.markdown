---
layout: blog
title: "Cucumber, WebRat, subdomain_fu together"
summary: Using Cucumber, WebRat, subdomain_fu together caused a weird redirect
---

After setting a subdomain via suddomain_fu, you may find that WebRat reports "You are being redirected", which might not be what you want.  WebRat expects to run tests against the domain "example.com" and it doesn't want tests to redirect outside of the test domain.

The solution is to create a step to change the subdomain:

{% highlight ruby linenos %}
Given /^we're on the subdomain "(.+?)"$/ do |subdomain|
  host! "#{subdomain}.example.com"
end
{% endhighlight %}
