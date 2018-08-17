---
layout: post
title:  "Shopify: Displaying the menu"
date:   2018-08-17 10:00:00 +0300
categories: shopify
lang: eng
autor:  glivera-team
---

In this article we will speak about  how to display the navigation in Shopify. Let’s display such menu:
<img alt="" src="../../../../i/output-menu-1.jpg">

## 1. Creation of the menu in the admin panel

So, the first we need to do is to create the menu in our admin panel. For this we go to <b>Navigation</b> tab and click <b>Add menu</b> as shown on the screenshot:

<img alt="" src="../../../../i/output-menu-2.jpg">

Add there our links clicking <b>Add menu item</b> and put the necessary url. Also, we gave the name to our menu in the field <b>Title</b>.

The result should be the following:
<img alt="" src="../../../../i/output-menu-3.jpg">

There is <b>handle</b> in each created menu. Due to this we can refer to the specific menu that we need. This <b>handle</b> we can proscribe manually:
<img alt="" src="../../../../i/output-menu-4.jpg">

After we had added some menu items, click <b>Save menu</b> and our menu will be created. Next what we need, is to bring the menu in our structure.

## 2. Creation of the menu structure

Let’s create the proper structure for our menu. The structure will be like this:

{% highlight html %}
<nav class="header_menu">
	<ul class="header_menu_list">
			<li class="header_menu_item">
				<a href="#" class="header_menu_link"></a>
			</li>
	</ul>
</nav>
{% endhighlight %}

The next we need to do is to read out with cycle all our links that lie in the folder <b>Main menu</b>.

Add the following code to our `ul`:

{% highlight html %}
<ul class="header_menu_list">
	{% raw  %}
	{% for link in linklists.main-menu.links %}
		<li class="header_menu_item">
			<a href="{{ link.url }}" class="header_menu_link">{{ link.title }}</a>
		</li>
	{% endfor %}
	{% endraw %}
</ul>
{% endhighlight %}

Let’s analyze the code a bit more. <b>linklists</b> includes all the menus that were created in the admin panel. <b>linklist.links</b> returns an array of links of a specific menu. Here you can see the <b>handle</b> that we prescribed in our admin panel - <b>main-menu</b>.

<b>'linklists.main-menu.links'<b> here we pull out all the links that are in the <b>'main-menu'<b>. The same we can do with other menus that were created in the admin panel, we just need to set the proper <b>'handle'<b>, and here we simply write down the name of the link and its url.

As a bonus, let’s do one more thing and change our link a bit:

{% highlight html %}
{% raw %}
<a {% if link.active %} class="header_menu_link active_mod" {% endif %} href="{{ link.url }}" class="header_menu_link">{{ link.title }}</a>
{% endraw %}
{% endhighlight %}

At this place we do the checking and if the link is active, we add to it the corresponding class <b>'active_mod'</b>.

That’s all.