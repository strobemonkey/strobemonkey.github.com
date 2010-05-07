---
layout: blog
title: "Creating a custom Spree report"
summary: How to create a custom report in Spree 0.10.2
---

Any additions you make to your Spree project should be stored in
`vendor/extensions/site/`
When Rails is up and running, code placed here will be rolled into Spree. If you update Spree in the future then your custom code shouldn't be affected if it's placed here.

## Controller ##

The controller for the new report will be stored in
`vendor/extensions/site/controllers/admin/`
The controller code is based upon the reports controller found in the Spree code base here
`app/controllers/admin/reports_controller.rb`
You'll need to create a new method, something like this
{% highlight ruby linenos %}
class Admin::ReportsController < Admin::BaseController
  before_filter :load_data  
  
  AVAILABLE_REPORTS = {
    :sales_total => {:name => "Sales Total", :description => "Sales Total For All Orders"},
    :backorders_total => {:name => "Backorders Total", :description => "Current Backorders Total"}
  }

  def index
    @reports = AVAILABLE_REPORTS
  end

  def sales_total
    @search = Order.searchlogic(params[:search])
    @search.checkout_complete = true
    #set order by to default or form result
    @search.order ||= "descend_by_created_at"
    
    @orders = @search.find(:all)    
  
    @item_total = @search.sum(:item_total)
    @charge_total = @search.sum(:adjustment_total)
    @credit_total = @search.sum(:credit_total)
    @sales_total = @search.sum(:total)
  end

  def backorders_total
    @products = Product.all.map { |p| if p.master.on_backorder > 0 then p end }.compact
  end

  private 
  def load_data
  end  

end
{% endhighlight %}

## View ##

The view for the report will be stored in 
`vendor/extensions/site/app/views/admin/reports/`
This view should be based upon the reports view found in the Spree code base
`vendor/extensions/theme_default/app/views/admin/reports/sales_total.html.erb`
Here's a custom report view (written in HAML which is included in Spree) called
`vendor/extensions/site/app/views/admin/reports/backorders_total.html.haml`
{% highlight ruby linenos %}
%h1= t("sales_totals")
%table.admin-report
  %thead
    %tr
      %th= t("name")
      %th= t("amount")
  %tbody
    - @products.each do |product|
      %tr
        %th(scope="row")= product.name
        %td(align="right")= product.variant.on_backorder
{% endhighlight %}

## View index ##

You'll also need to override the default reports index view
`vendor/extensions/theme_default/app/views/admin/reports/index.html.erb`
by creating a new
`vendor/extensions/site/app/views/admin/reports/index/html.erb`
like this
{% highlight ruby linenos %}
%h1= t("listing_reports")
%table.index
  %thead
    %tr
      %th= t("name")
      %th= t("description")
  %tbody
    - @reports.each do |key, value|
      %tr
        %td= link_to t(value[:name].downcase.gsub(" ","_")), send("#{key}_admin_reports_url".to_sym)
        %td= t(value[:description].downcase.gsub(" ","_"))
{% endhighlight %}

## Route ##

You'll need to add some routing information for the new report into
`vendor/extensions/site/config/routes.rb`
and it should look a bit like this
{% highlight ruby linenos %}
ActionController::Routing::Routes.draw do |map|
   map.backorders_total_admin_reports '/admin/reports/backorders_total', :controller => 'admin/reports', :action => 'backorders_total'
end
{% endhighlight %}

## Locale ##

As Spree can be configured for many different languages, not just English, we need to add phrase lookups for our new report into
`vendor/extensions/site/config/locales/en-US.yml`
Ours will look like this
{% highlight ruby linenos %}
---
en-US:
  backorders_total: "Backorders Total"
  current_backorders_total: "Current Backorders Total"
{% endhighlight %}