---
layout: post
title:  "Shopify: password recovery and reset"
date:   2018-05-10 10:00:00 +0300
categories: shopify
lang: en
autor:  glivera-team
---


In this article we will learn how to create the page and the template for password recovery and reset, and we will edit the file <br>reset_password.liquid</b>. We are going to work with such form:
<img alt="Shopify: password recovery and reset" src="../../../../i/restore-password-1.eng.jpg">


## 1.Creating the template 
As we don’t have the template for password recovery in Shopify, we will need to make it. It can be done via the admin panel. To do this we go to the tab <b>‘Themes’</b>, select current theme, click the button <b>‘Actions’</b>, and choose <b>‘Edit code’</b>.
<img alt="Shopify: password recovery and reset" src="../../../../i/restore-password-2.jpg">


After that we create a new template using button <b>‘Add a new template’</b>, select the template <b>‘page’</b> from the list, and name it <b>‘restore’</b>, then we click on <b>‘Create template’</b>.
<img alt="sShopify: password recovery and reset" src="../../../../i/restore-password-3.jpg">


Next what we need to do is to create the page for password recovery, to do this we go to the admin panel in <b>‘Pages’</b> and create the new page by clicking the button <b>‘Add page’</b>.

Enter the name of the page, in the option <b>‘Template’</b> select the template that we have just created, it’s <b>‘page.restore’</b>. After that we save our page.
<img alt="Shopify: password recovery and reset" src="../../../../i/restore-password-4.eng.jpg">


## 2.Creating the structure
Next step we need to do is to add the structure for our form. Open the template <b>page.restore</b> and add the following code there: 

{% highlight html %}
<h2 class="title_h2">Password recovery</h2>
<div class="form_wrap">
	<ul class="input_list">
		<li class="input_item">
			<div class="input_left">
				<label for="user_email" class="input_label">Your E-mail:</label>
				<div class="input_descr">Specified at registration</div>
			</div>
			<div class="input_right">
				<input id="user_email" type="email" placeholder="Enter your e-mail" name="email" class="form_field default_mod">
			</div>
		</li>
	</ul>
	<div class="btn_wrap">
		<button type="submit" class="btn">Submit</button>
	</div>
</div>
{% endhighlight %}

After the structure is made, we need to add the tag <b>‘form’</b> with the attribute <b>‘recover_customer_password’</b>. I think it should be clear what it is responsible for, considering its name . We should get the following: 

{% highlight html %}
{% raw %}
<h2 class="title_h2">Password recovery</h2>
{% form 'recover_customer_password' %}
	<div class="form_wrap">
		<ul class="input_list">
			<li class="input_item">
				<div class="input_left">
					<label for="user_email" class="input_label">Your E-mail:</label>
					<div class="input_descr">Specified at registration</div>
				</div>
				<div class="input_right">
					<input id="user_email" type="email" placeholder="Enter your e-mail" name="email" class="form_field default_mod">
				</div>
			</li>
		</ul>
		<div class="btn_wrap">
			<button type="submit" class="btn">Submit</button>
		</div>
	</div>
{% endform %}
{% endraw %}
{% endhighlight %}

Now let’s go to <b>your-store-name.myshopify.com/pages/restore</b>

After that, enter your <b>email</b> in the input (the account must already exist), and click <b>‘Submit’</b>. Thereafter, you will find this message in your e-mail: 
<img alt="Shopify: password recovery and reset" src="../../../../i/restore-password-5.jpg">


Of course, the store will have another name. For now, we don’t click <b>‘Reset your password’</b>.

## 3.Creating the structure for the template reset_password.liquid
The file <b>reset_password.liquid</b> is responsible for password reset. If you don’t have it, you can create it via the admin panel by selecting file <b>‘reset_password’</b>
<img alt="Shopify: password recovery and reset" src="../../../../i/restore-password-6.jpg">

If there is some information, you need to delete it and add the following code:

{% highlight html %}
{% raw %}
<h2 class="title_h2">New password</h2>
{% form 'reset_customer_password' %}
	<div class="form_wrap">
		<ul class="input_list">
			<li class="input_item">
				<div class="input_left">
					<label for="user_email" class="input_label">Your new password:</label>
					<div class="input_descr">Think of a password consisting of at least eight symbols</div>
				</div>
				<div class="input_right">
					<input id="user_pass" type="password" placeholder="Think of a password" name="customer[password]" class="form_field default_mod">
					<input type="password" placeholder="Repeat a password" name="customer[password_confirmation]" class="form_field default_mod">
				</div>
			</li>
		</ul>
		<div class="btn_wrap">
			<button type="submit" class="btn">Enter</button>
		</div>
	</div>
{% endform %}
{% endraw %}
{% endhighlight %}

Here we have added a little bit more additional structure + added the form with attribute <b>‘reset_customer_password’</b>, which is responsible for password reset. Also, the second field <b>customer[password_confirmation]</b> must have inputs with attributes <b>name</b> with values <b>customer[password]</b>. After that we save our file. Now log on your e-mail and click the button <b>‘Reset your password’</b>, then we get to the template <b>reset_password.liquid</b> that we have just edited, here you should see: 

<img alt="Shopify: password recovery and reset" src="../../../../i/restore-password-7.eng.jpg">


If you don’t have any styles, of course, the form will look totally different, but we will not spend time on adding the styles in this article. After you have entered new password and submitted, you will log on your account and your password will be changed. 
