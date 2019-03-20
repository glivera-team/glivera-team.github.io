---
layout: post
title:  "Shopify: registration of a new user"
date:   2018-05-03 10:00:00 +0300
categories: shopify
lang: en
autor:  glivera-team
---

Let’s find out how to register the new user in <b>Shopify</b>. We will work with this form:
<img alt="" src="../../../../i/create-account-1.eng.png">


We have the template, which is responsible for registration of the new user, and it is located in <b>templates/customers/register.liquid</b>. If you don’t have it, you can create this file via the admin panel in the theme editor (it goes by default in slate, but it’s a completely different story).

## 1.Creation of register.liquid file

Let’s go to your current theme in the admin panel, click <b>Actions > Edit code</b>.
<img alt="" src="../../../../i/create-account-2.png">

After that, we create a new template by clicking the button <b>‘Add a new template’</b>, select <b>customers/register</b> file from the drop-down list and click <b>‘Create template’</b>.
<img alt="" src="../../../../i/create-account-3.png">
Delete everything that you see in this file. 

## 2.Creation of a structure

So, the first step we need to do is to create an appropriate structure. 

Add the following code in the register.liquid file:

{% highlight html %}
<h3 class="form_title">Personal information</h3>
<ul class="form_list">
	<li class="form_item">
		<label for="first_name" class="form_label">First name:</label>
		<input type="text" placeholder="First name" id="first_name" class="default_input">
	</li>
	<li class="form_item">
		<label for="last_name" class="form_label">Last name:</label>
		<input type="text" placeholder="Last name" id="last_name" class="default_input">
	</li>
	<li class="form_item">
		<label for="email" class="form_label">Your E-mail:</label>
		<input type="email" placeholder="E-mail" id="email" class="default_input">
	</li>
	<li class="form_item">
		<label for="password" class="form_label">Password</label>
		<input type="password" placeholder="Password" id="password" class="default_input">
	</li>
	<li class="form_item right_mod">
		<input class="btn" type="submit" value="Submit">
	</li>
</ul>
{% endhighlight %}

In <b>Shopify</b> we have a lot of forms for different needs. For example, the form for registration of a new user, authorization form, account activation form, and so on. In this article we will review the form, which is designed to register a new user. The next what we need to do is to add the form to our markup, and you can do this with form tag. 

{% highlight html %}
{% raw  %}
{% form 'create_customer' %} 

{% endform % }
{% endraw %}
{% endhighlight %}

The result should be as follows:

{% highlight html %}
{% raw %}
<h3 class="form_title">Personal information</h3>
{% form 'create_customer' %}
	<ul class="form_list"
		<li class="form_item">
			<label for="first_name" class="form_label">First name:</label>
			<input type="text" placeholder="First name" id="first_name" class="default_input">
		</li>
		<li class="form_item">
			<label for="last_name" class="form_label">Last name:</label>
			<input type="text" placeholder="Last name" id="last_name" class="default_input">
		</li>
		<li class="form_item">
			<label for="email" class="form_label">Your E-mail:</label>
			<input type="email" placeholder="E-mail" id="email" class="default_input">
		</li>
		<li class="form_item">
			<label for="password" class="form_label">Password</label>
			<input type="password" placeholder="Password" id="password" class="default_input">
		</li>
		<li class="form_item right_mod">
			<input class="btn" type="submit" value="Submit">
		</li>
	</ul>
{% endform %}
{% endraw %}
{% endhighlight %}

Here we wrap our markup with the tag form, which is responsible for creating a new user. Next step is to add attribute <b>name</b> with appropriate values in our inputs.

We should get following:

{% highlight html %}
{% raw %}
<h3 class="form_title">Personal information</h3>
{% form 'create_customer' %}
	<ul class="form_list"
		<li class="form_item">
			<label for="first_name" class="form_label">First name:</label>
			<input type="text" placeholder="First name" name="customer[first_name]" id="first_name" class="default_input">
		</li>
		<li class="form_item">
			<label for="last_name" class="form_label">Last name:</label>
			<input type="text" placeholder="Last name" name="customer[last_name]" id="last_name" class="default_input">
		</li>
		<li class="form_item">
			<label for="email" class="form_label">Your E-mail:</label>
			<input type="email" placeholder="E-mail" name="customer[email]" id="email" class="default_input">
		</li>
		<li class="form_item">
			<label for="password" class="form_label">Password</label>
			<input type="password" placeholder="Password" name="customer[password]" id="password" class="default_input">
		</li>
		<li class="form_item right_mod">
			<input class="btn" type="submit" value="Submit">
		</li>
	</ul>
{% endform %}
{% endraw %}
{% endhighlight %}

Here we simply describe each field what is responsible for. To see the form we have just created, go to <b>yourshop-name.com/account/register</b>. After filling all the fields and clicking <b>Submit</b>, your account should be created. To check whether it is so, enter into the section <b>Customers</b> in your admin panel and look if there is the account you have just created in the list. 

That’s all, our registration form is completely done. You need only to stylize it and everything will be perfect. 
