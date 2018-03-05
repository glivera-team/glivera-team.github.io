---
layout: post
title:  "Shopify: menu output"
date:   2017-11-17 9:25:00 +0300
categories: shopify
lang: en
autor:  glivera-team
---

In this article, we'll look at how to display navigation in Shopify. We will deduce here such menu in a site header:
<img alt="" src="../../../../i/output-menu-1.jpg">

## 1.Create a menu in the admin panel

And so the first thing we need to do is create a menu in our admin panel. To do this, go to the admin area, go to the tab <b>'Navigation'</b> and click on the button <b>'Add menu'</b> as shown in the screenshot:
<img alt="" src="../../../../i/output-menu-2.jpg">

Add our links by clicking on the button <b>'Add menu item'</b>, and put the desired url. Also give the name of our menu in the field <b>'Title'</b>.

You should get the following:
<img alt="" src="../../../../i/output-menu-3.jpg">

Each menu created has its own menu. <b>'handle'</b>. Thanks to this, we can refer to the specific menu that we need. This <b>'handle'</b> can also be written manually:
<img alt="" src="../../../../i/output-menu-4.jpg">

After we added several menu items, click on the button <b>'Save menu'</b>, our menu will be created. The next thing we need is to bring this menu to our structure.

## 2.Creating a structure under the menu

Let's create a suitable structure for our menu.
Our structure will be as follows:

{% highlight html %}
<nav class="header_menu">
	<ul class="header_menu_list">
			<li class="header_menu_item">
				<a href="#" class="header_menu_link"></a>
			</li>
	</ul>
</nav>
{% endhighlight %}

The next thing we need to do is cycle out all of our links that we have in our <b>‘Main menu’</b>.

Add the following code to our ul:

{% highlight html %}
<ul class="header_menu_list">
	{% for link in linklists.main-menu.links %}
		<li class="header_menu_item">
			<a href="{{ link.url }}" class="header_menu_link">{{ link.title }}</a>
		</li>
	{% endfor %}
</ul>
{% endhighlight %}

Let's analyze the code a bit more. <b>'linklists'</b> includes all the menus that you created in the admin panel. <b>'linklist.links'</b> returns an array of links for a specific menu. Here you can see that <b>'handle'</b> which we asked in our admin panel - <b>'main-menu'</b>.

<b>'linklists.main-menu.links'</b> Here we pull out all the links that are in the <b>'main-menu'</b>. The same conclusion we can do with other menus created by us in the admin panel, just need to substitute the necessary <b>'handle'</b>. <b>{{ link.url }}</b> and <b>{{ link.title }}</b> here we just substitute the name of the link and its url.

Bonus let's do one more thing, change a bit of our link:

{% highlight html %}
<a {% if link.active %} class="header_menu_link active_mod" {% endif %} href="{{ link.url }}" class="header_menu_link">{{ link.title }}</a>
{% endhighlight %}

Here we do a test, if the reference to which we are active, then add to it the corresponding class <b>'active_mod'</b>.

This is all for today.