---
layout: post
title:  "Shopify: add the site search"
date:   2018-04-26 10:00:00 +0300
categories: shopify
lang: en
autor:  glivera-team
---

In this article we will edit the template <b>search.liquid</b> where the user can find the store goods, pages, and blog articles. 

At the result you should get the following:
<img alt="" src="../../../../i/search-1.jpg">


Now let’s go to <b>“yourshop-name.com/search”</b> to see the search page. 

## 1.Adding the search form

The first we need to do is to add the search form. Open the file <b>search.liquid</b>, and change its content on the following code: 

{% highlight html %}
<h2 class="title_h2">Search</h2>
<div class="search">
	<form action="/search" method="get" class="search_form">
		<div class="search_row">
			<input class="search_input" type="text" name="q" value="Search">
			<input type="submit"  class="search_btn">
		</div>
	</form>
</div>
{% endhighlight %}

Now on the page you can see this simple form: 
<img alt="" src="../../../../i/search-2.eng.jpg">

The interface of the form will depend on whether you have prescribed some styles for it or not, but in this article we will not focus on the styles.

## 2.Checking whether the search was completed

Before bringing up the content of the search, it will be usefull to do a good check whether the search request was actually sent or not. We do this to avoid searching for empty strings when you click from search page. Add the code below after block with form: 

{% highlight html %}
{% raw  %}
{% if search.performed %}

{% endif %}
{% endraw %}
{% endhighlight %}

We should get the following:
<img alt="" src="../../../../i/search-3.jpg">


For now, don’t pay attention on the <b>paginate search.results</b> code, we will talk about it later. 

The code <b>if search.performed</b> returns true, if the form with attribute action = / search was successfully sent. 

## 3.Display the search result through a cyclic path

Now let’s add the cycle <b>for</b> to display the search result. We want to look through the <b>«results»</b> array, which is the part of the <b>search</b> object, to see whether we have something to display or not. 
Enter the following code inside the construction we have added above: 

{% highlight html %}
{% raw %}
{% if search.performed %}
	{% if search.results_count == 0 %}
		<div class="search_none">Your request <span class="search_none_result">"{{ search.terms }}"</span> no results.</div>
	{% else %}
		<ul class="search_result">
			{% for item in search.results %}
				<li class="search_result_item">
					<div class="search_result_title">{{ item.title | link_to: item.url | replace: 'href', 'class="search_result_link" href' }}</div>
					{% if item.featured_image %}
						<div class="product">
							<a class="product_i_link" href="{{ item.url }}">
								<img class="product_img" src="{{ item.featured_image.src | product_img_url: 'small' }}" alt="{{ product.featured_image.alt | escape}}">
							</a>
						</div>
						<div class="search_result_text">{{ item.content | strip_html | truncatewords: 40 | highlight: search.terms }}</div>
						{% else %}
							<div class="search_result_text">{{ item.content | strip_html | truncatewords: 40 | highlight: search.terms }}</div>
					{% endif %}
				</li>
			{% endfor %}
		</ul>
	{% endif %}
{% endif %}
{% endraw %}
{% endhighlight %}

We should get the following:
<img alt="" src="../../../../i/search-4.eng.jpg">

Now, using the form we have made earlier, let’s find the text <b>‘lorem’</b>, I have it in the articles of the blog.

The search result will be the following:
<img alt="" src="../../../../i/search-1.jpg">

The result of the search, mainly our text <b>‘lorem’</b>, wraps into the tag strong with <b>highlight</b> level, and we can stylize this level.

This highlightning is done with filter <b>highlight</b>.

More additional information about filtres you can find in documentation: https://help.shopify.com/themes/liquid/filters/additional-filters
More about the object search: https://help.shopify.com/themes/liquid/objects/search

Let’s speak about the code more detailed. 

{% highlight html %}
{% raw %}
{% if search.results_count == 0 %}
{% endraw %}
{% endhighlight %}

If there is no result, you will see <b>div</b> with the text that  there is nothing on this request. 
{% highlight html %}
{% raw %}
{% if item.featured_image %}
{% endraw %}
{% endhighlight %}

This code checks if the search result has <b>featured_image</b> (means the image of the product). It checks whether the product is the result of the search or not. 

If the search result is a product, then we want to display a proper image and a description to it. If we prescribe the name of the product in search, we will have the following picture: 
<img alt="" src="../../../../i/search-6.jpg">

If the search result isn’t a product, we display only page text or the articles of the blog. Also, if you don’t have any searching results, you will see next message:  
<img alt="" src="../../../../i/search-7.eng.jpg">


## 4.Adding the pagination for search results

{% highlight html %}
{% raw %}
{% paginate search.results by 5 %}

{% endpaginate %}
{% endraw %}
{% endhighlight %}

Here we point that want to see only 5 search results, the rest will be on the other pages. 
<img alt="" src="../../../../i/search-5.eng.jpg">

What does <b>include ‘pagination’</b> mean?
{% highlight html %}
{% raw %}
{% include 'pagination' %}
{% endraw %}
{% endhighlight %}

The snippets that are in the folder <b>Snippets</b> with the resolution liquid connect in this way, in particulary I have there <b>pagination.liquid</b> file. We won’t speak about the code of pagination because it’s quite easy, but you can find information about it [here]https://gist.github.com/jsnord/69ce7677efc08886e319bfbc2529878c

## 5.Conclusion

You can add this form with search wherever you want, for example, in header, in footer, it doesn’t matter, the main is to have <b>‘action=”/search” method=”get”’</b> in the attributes, and the input must have the attribute name with the value <b>“q”<b>. If you have this, everything will work well.  
