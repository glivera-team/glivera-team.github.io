---
layout: post
title:  "Shopify: добавление дополнительной информации о пользователе"
date:   2018-05-16 12:00:00 +0300
categories: shopify
lang: ru
autor:  glivera-team
---

В этой статье мы добавим форму с возможностью добавление более подробного описания информации о пользователе - номер телефона, адрес, название компании и т.д

Создадим вот такую форму, где пользователь может добавить о себе дополнительную информацию, заниматься оформлением этой формы в этой статье мы не будем.

<img alt="" src="../../../../i/add-info-customer-1.jpg">

Итак приступим. За добавление дополнительной информации о пользователе у нас ответственен шаблон <b>addresses.liquid</b>

# 1.Создание шаблона

Если у вас нет этого шаблона, этот шаблон можно создать через админку. Для этого переходим в редактор темы, нажимаем на кнопку <b>‘Add a new template’</b> и создаем шаблон <b>addresses.liquid</b>
<img alt="" src="../../../../i/add-info-customer-2.jpg">

## 2.Создание структуры

Вставьте код который приведен ниже в файл <b>addresses.liquid</b> предварительно удалив все то что там есть:

{% highlight html %}
<div class="form_wrap">
	<h3 class="form_title">Информация о компании</h3>
	<ul class="input_list">
		<li class="input_item">
			<dl class="form_cell">
				<dt class="form_cell_title">
					<label for="user_company">Название компании:</label>
				</dt>
				<dd class="form_field_wrap">
					<input type="text" id="user_company" placeholder="Введите полное название компании" name="address[company]" class="form_field">
				</dd>
			</dl>
		</li>
		<li class="input_item">
			<dl class="form_cell">
				<dt class="form_cell_title">
					<label for="user_company">Адрес компании:</label>
				</dt>
				<dd class="form_field_wrap">
					<input type="text" id="user_company" placeholder="Адрес компании" name="address[address1]" class="form_field ">
				</dd>
			</dl>
		</li>
		<li class="input_item">
			<dl class="form_cell">
				<dt class="form_cell_title">
					<label for="user_phone">Телефон:</label>
				</dt>
				<dd class="form_field_wrap">
					<input type="tel" id="user_phone" placeholder="+7 (XXX) XXX-XX-XX" name="address[phone]" class="form_field">
				</dd>
			</dl>
		</li>
		<li class="input_item">
			<dl class="form_cell">
				<dt class="form_cell_title form_v2_mod">
					<label for="user_inn">ИНН:</label>
				</dt>
				<dd class="form_field_wrap">
					<input type="text" id="user_inn" placeholder="ХХХХХХХХХХ" name="address[zip]" class="form_field">
				</dd>
			</dl>
		</li>
		<li class="input_item row_mod right_mod">
			<label class="radio_check">
				<input required name="check" type="checkbox" checked="checked" class="radio_check_input"><span class="radio_check_text">Я согласен с правилами пользования сайтом</span>
			</label>
		</li>
	</ul>
	<div class="btn_wrap">
		<button type="submit" class="btn">Отправить</button>
	</div>
</div>
{% endhighlight %}

Здесь самое главное прописать в инпуты нужные атрибуты <b>name</b>. Более подробно какие есть возможные значения у атрибутов <b>name</b> можно почитать в документации - https://help.shopify.com/themes/development/templates/customers-addresses

Следующее что нам нужно сделать, это добавить нужную форму которая ответственна за добавление дополнительной информации. За это у нас ответственна форма:
{% highlight html %}
{% raw %}
{% form 'customer_address', customer.new_address %}
{% endraw %}
{% endhighlight %}

Добавьте следующий код:

{% highlight html %}
{% raw %}
{% form 'customer_address', customer.new_address %}
	<div class="form_wrap">
		<h3 class="form_title">Информация о компании</h3>
		<ul class="input_list">
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_company">Название компании:</label>
					</dt>
					<dd class="form_field_wrap">
						<input type="text" id="user_company" placeholder="Введите полное название компании" name="address[company]" class="form_field ">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_company">Адрес компании:</label>
					</dt>
					<dd class="form_field_wrap">
						<input type="text" id="user_company" placeholder="Адрес компании" name="address[address1]" class="form_field ">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_phone">Телефон:</label>
					</dt>
					<dd class="form_field_wrap">
						<input type="tel" id="user_phone" placeholder="+7 (XXX) XXX-XX-XX" name="address[phone]" class="form_field">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title form_v2_mod">
						<label for="user_inn">ИНН:</label>
					</dt>
					<dd class="form_field_wrap">
						<input type="text" id="user_inn" placeholder="ХХХХХХХХХХ" name="address[zip]" class="form_field">
					</dd>
				</dl>
			</li>
			<li class="input_item row_mod right_mod">
				<label class="radio_check">
					<input required name="check" type="checkbox" checked="checked" class="radio_check_input"><span class="radio_check_text">Я согласен с правилами пользования сайтом</span>
				</label>
			</li>
		</ul>
		<div class="btn_wrap">
			<button type="submit" class="btn">Отправить</button>
		</div>
	</div>
{% endform %}
{% endraw %}
{% endhighlight %}

Теперь перейдем по адресу <b>your-store-name.myshopify.com/account/addresses</b> и заполним ту форму которую мы только что создали, после заполнения нажмем на кнопку <b>‘Отправить’</b>. После того как мы добавили какую-то информацию в эти поля, просмотреть мы сможем их через админку во вкладке <b>'Customers'</b> выбрав свой аккаунт и в блоке <b>'DEFAULT ADDRESS'</b> будет отображена та информация которую мы ввели в форму.

<img alt="" src="../../../../i/add-info-customer-3.jpg">

Также еще проставим в инпуты следующие значения:

{% highlight html %}
{% raw %}
{% form 'customer_address', customer.new_address %}
	<div class="form_wrap">
		<h3 class="form_title">Информация о компании</h3>
		<ul class="input_list">
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_company">Название компании:</label>
					</dt>
					<dd class="form_field_wrap">
						<input value="{{ customer.default_address.company }}" type="text" id="user_company" placeholder="Введите полное название компании" name="address[company]" class="form_field ">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_company">Адрес компании:</label>
					</dt>
					<dd class="form_field_wrap">
						<input value="{{ customer.default_address.address1 }}" type="text" id="user_company" placeholder="Адрес компании" name="address[address1]" class="form_field ">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title">
						<label for="user_phone">Телефон:</label>
					</dt>
					<dd class="form_field_wrap">
						<input value="{{ customer.default_address.phone }}" type="tel" id="user_phone" placeholder="+7 (XXX) XXX-XX-XX" name="address[phone]" class="form_field">
					</dd>
				</dl>
			</li>
			<li class="input_item">
				<dl class="form_cell">
					<dt class="form_cell_title form_v2_mod">
						<label for="user_inn">ИНН:</label>
					</dt>
					<dd class="form_field_wrap">
						<input value="{{ customer.default_address.zip }}" type="text" id="user_inn" placeholder="ХХХХХХХХХХ" name="address[zip]" class="form_field">
					</dd>
				</dl>
			</li>
			<li class="input_item row_mod right_mod">
				<label class="radio_check">
					<input required name="check" type="checkbox" checked="checked" class="radio_check_input"><span class="radio_check_text">Я согласен с правилами пользования сайтом</span>
				</label>
			</li>
		</ul>
		<div class="btn_wrap">
			<button type="submit" class="btn">Отправить</button>
		</div>
	</div>
{% endform %}
{% endraw %}
{% endhighlight %}

Это делается для того, чтобы когда мы уже заполнили эти поля и отправили форму, в следующий раз когда мы захотим перейти на страницу <b>addresses</b> снова, то мы уже увидим следующую картину:
<img alt="" src="../../../../i/add-info-customer-4.jpg">

То есть мы просто здесь подставили те значение которые мы заполнили до этого.