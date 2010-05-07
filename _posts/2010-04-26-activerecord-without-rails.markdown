---
layout: blog
title: "Using ActiveRecord without Rails"
summary: You can use ActiveRecord without Rails but you'll have to do a bit of frigging about to get all the functionality you want
---

You can add ActiveRecord functionality to your classes by adding the following, assuming that you've got the ActiveRecord gem on your machine
{% highlight ruby linenos %}
require 'active_record'
class Widget < ActiveRecord::Base
end
{% endhighlight %}

In your client you'll need to establish a connection to your database with something along these lines
{% highlight ruby linenos %}
dbconfig = YAML::load(File.open(File.dirname(__FILE__) + '/../config/database.yml'))
ActiveRecord::Base.establish_connection(dbconfig)
{% endhighlight %}

You can even have migrations using a Rake file in your project root that looks a bit like this
{% highlight ruby linenos %}
require 'active_record'
require 'yaml'

task :default => :migrate

desc "Migrate the database through scripts in db/migrate. Target specific version with VERSION=x"
task :migrate => :environment do
  ActiveRecord::Migrator.migrate('db/migrate', ENV["VERSION"] ? ENV["VERSION"].to_i : nil )
end

task :environment do
  ActiveRecord::Base.establish_connection(YAML::load(File.open('config/database.yml')))
  ActiveRecord::Base.logger = Logger.new(File.open('log/database.log', 'a'))
end
{% endhighlight %}

But if you're looking for the fancier functionality such as ActsAsList or ActsAsTree then you're going to have to do a bit of frigging as this functionality was removed from ActiveRecord (in Rails version 2.3.somethingorother) to make it more lightweight. The quickest way is to create a blank Rails project and install the desired functionality as a plugin
`script/plugin install acts_as_tree`
then copy the code from
`vendor/plugins/acts_as_tree/lib/activerecord/acts/tree.rb`
and place it into your project
`lib/activerecord/acts/tree.rb`
then in your model add the following
{% highlight ruby linenos %}
$:.unshift "#{File.dirname(__FILE__)}/../lib"
require 'active_record/acts/list'
ActiveRecord::Base.class_eval { include ActiveRecord::Acts::List }
{% endhighlight %}