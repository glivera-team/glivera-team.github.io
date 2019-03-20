---
layout: post
title:  "Shopify: adding additional information about the user"
date:   2018-05-16 12:00:00 +0300
categories: shopify
lang: en
autor:  glivera-team
---

In this article we will add a form with the ability to add a more detailed description of the user – phone number, address, company name, and so on.

Let’s create a form where the user can write additional information about himself, but we will not model this form in this article. 

<img alt="" src="../../../../i/add-info-customer-1.eng.jpg">

So, let’s start. We have special template <b>addresses.liquid</b>, which is responsible for adding additional information about the user.

# 1.Creating a template

If you don’t have this template, you can create it via the admin panel. For this we go to the theme editor, click the button <b>‘Add a new template’</b>, and create <b>addresses.liquid</b> template.
<img alt="" src="../../../../i/add-info-customer-2.jpg">

## 2.Creating a structure


Paste the code below in the file <b>addresses.liquid</b>, previously removing all that you have there. 

{% highlight html %}
<div class="form_wrap">
	<h3 class="form_title">Information about the company</h3>
	<ul class="input_list">
		<li class="input_item">
			<dl class="form_cell">
				<dt class="form_cell_title">
					<label for="user_company">Company name:</label>
				</dt>
				<dd class="form_field_wrap">
					<input type="text" id="user_company" placeholder="Enter the full name of the company" name="address[company]" class="form_field">
				</dd>
			</dl>
		</li>
		<li class="input_item">
			<dl class="form_cell">
				<dt class="form_cell_title">
					<label for="user_company">Company address:</label>
				</dt>
				<dd class="form_field_wrap">
					<input type="text" id="user_company" placeholder="Company address" name="address[address1]" class="form_field ">
				</dd>
			</dl>
		</li>
		<li class="input_item">
			<dl class="form_cell">
				<dt class="form_cell_title">
					<label for="user_phone">Phone number:</label>
				</dt>
				<dd class="form_field_wrap">
					<input type="tel" id="user_phone" placeholder="+7 (XXX) XXX-XX-XX" name="address[phone]" class="form_field">
				</dd>
			</dl>
		</li>
		<li class="input_item">
			<dl class="form_cell">
				<dt class="form_cell_title form_v2_mod">
					<label for="user_inn">INN:</label>
				</dt>
				<dd class="form_field_wrap">
					<input type="text" id="user_inn" placeholder="ХХХХХХХХХХ" name="address[zip]" class="form_field">
				</dd>
			</dl>
		</li>
		<li class="input_item row_mod right_mod">
			<label class="radio_check">
				<input required name="check" type="checkbox" checked="checked" class="radio_check_input"><span class="radio_check_text">I agree with the terms of the use of the site</span>
			</label>
		</li>
	</ul>
	<div class="btn_wrap">
		<button type="submit" class="btn">Submit</button>
	</div>
</div>

{% endhighlight %}

The most important here is to prescribe required attributes <b>name</b> in inputs. You can read more detailed about the possible values of attributes <b>name</b> in following documentation - https://help.shopify.com/themes/development/templates/customers-addresses

Next step we should do is to add required form, which is responsible for adding additional information. We have the special form for this: 

{% highlight html %}
{% raw %}
{% form 'customer_address', customer.new_address %}
{% endraw %}
{% endhighlight %}

Add the following code:

{% highlight html %}
{% raw %}
{% form 'customer_address', customer.new_address %}
	<div class="form_wrap">
		<h3 class="form_title">Information about the company</h3>
		<ul class="input_list">
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_company">Company name:</label>
					</dt>
					<dd class="form_field_wrap">
						<input type="text" id="user_company" placeholder="Enter the full name of the company" name="address[company]" class="form_field ">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_company">Company address:</label>
					</dt>
					<dd class="form_field_wrap">
						<input type="text" id="user_company" placeholder="Company address" name="address[address1]" class="form_field ">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_phone">Phone number:</label>
					</dt>
					<dd class="form_field_wrap">
						<input type="tel" id="user_phone" placeholder="+7 (XXX) XXX-XX-XX" name="address[phone]" class="form_field">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title form_v2_mod">
						<label for="user_inn">INN:</label>
					</dt>
					<dd class="form_field_wrap">
						<input type="text" id="user_inn" placeholder="ХХХХХХХХХХ" name="address[zip]" class="form_field">
					</dd>
				</dl>
			</li>
			<li class="input_item row_mod right_mod">
				<label class="radio_check">
					<input required name="check" type="checkbox" checked="checked" class="radio_check_input"><span class="radio_check_text"> I agree with the terms of the use of the site </span>
				</label>
			</li>
		</ul>
		<div class="btn_wrap">
			<button type="submit" class="btn">Submit</button>
		</div>
	</div>
{% endform %}
{% endraw %}
{% endhighlight %}

Now let’s go to <b>your-store-name.myshopify.com/account/addresses</b> and fill the form we have just created, after filling, click the button “Submit”. After we have added some information, we can look through it via the admin panel in the tab <b>‘Customers’</b> by selecting your account, and in the block <b>‘DEFAULT ADDRESS’</b> will be displayed information we have filled in the form. 

<img alt="" src="../../../../i/add-info-customer-3.jpg">

We also need to write following values in the inputs:

{% highlight html %}
{% raw %}
{% form 'customer_address', customer.new_address %}
	<div class="form_wrap">
		<h3 class="form_title">Information about the company</h3>
		<ul class="input_list">
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_company">Company name:</label>
					</dt>
					<dd class="form_field_wrap">
						<input value="{{ customer.default_address.company }}" type="text" id="user_company" placeholder="Enter the full name of the company" name="address[company]" class="form_field ">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_company">Company address:</label>
					</dt>
					<dd class="form_field_wrap">
						<input value="{{ customer.default_address.address1 }}" type="text" id="user_company" placeholder="Company address" name="address[address1]" class="form_field ">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_phone">Phone number:</label>
					</dt>
					<dd class="form_field_wrap">
						<input value="{{ customer.default_address.phone }}" type="tel" id="user_phone" placeholder="+7 (XXX) XXX-XX-XX" name="address[phone]" class="form_field">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title form_v2_mod">
						<label for="user_inn">INN:</label>
					</dt>
					<dd class="form_field_wrap">
						<input value="{{ customer.default_address.zip }}" type="text" id="user_inn" placeholder="ХХХХХХХХХХ" name="address[zip]" class="form_field">
					</dd>
				</dl>
			</li>
			<li class="input_item row_mod right_mod">
				<label class="radio_check">
					<input required name="check" type="checkbox" checked="checked" class="radio_check_input"><span class="radio_check_text"> I agree with the terms of the use of the site </span>
				</label>
			</li>
		</ul>
		<div class="btn_wrap">
			<button type="submit" class="btn">Submit</button>
		</div>
	</div>
{% endform %}
{% endraw %}
{% endhighlight %}

We are doing this for when the next time we want to go to the <b>addresses</b> page again, after we have filled these fields and sent the form, we will see the following picture:  
<img alt="" src="../../../../i/add-info-customer-4.eng.jpg">

So we have just substituted values that we have filled in before.