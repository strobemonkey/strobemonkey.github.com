---
layout: blog
title: "CouchDB, MacPorts and admin user problem"
summary: Creating an admin user on CouchDB after installing via MacPorts
---

After installing CouchDB via MacPorts, running Futon shows a message about an admin party:

"Welcome to Admin Party! Everyone is admin. Fix this"

After clicking on the "fix this" link and supplying a new admin user name and password, the dialog box hangs around for ever.

The solution is to fiddle with ownerships and rights:

{% highlight bash %}
sudo chown -R couchdb:couchdb /opt/local/etc/couchdb/
sudo chmod -R 0770 /opt/local/etc/couchdb/
{% endhighlight %}

Then retry.