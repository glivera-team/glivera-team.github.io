---
layout: post
title:  "Shopify: регистрация нового пользователя"
date:   2018-05-03 10:00:00 +0300
categories: shopify
lang: ru
autor:  glivera-team
---

Разберем как сделать регистрацию нового пользователя в <b>Shopify</b>. Работать мы будем с такой формой.
<img alt="" src="../../../../i/create-account-1.png">

Шаблон <b>register.liquid</b> который находится по пути <b>templates/customers/register.liquid</b> он отвечает за регистрацию нового пользователя. Если его у вас нет, вы можете создать этот файл через свою админ-панель в редакторе темы(в slate он идет по умолчанию, но это уже совсем другая история).

## 1.Создание файла register.liquid

Переходим к вашей текущей теме в админке, нажимаем <b>Actions > Edit code</b>
<img alt="" src="../../../../i/create-account-2.png">

После этого создаем новый шаблон кликая по кнопке <b>‘Add a new template’</b> и из выпадающего списка выбираем файл <b>customers/register</b> после этого нажимаем на кнопку <b>‘Create template’</b>.
<img alt="" src="../../../../i/create-account-3.png">
Удалите все то что есть в этом файле.

## 2.Создание структуры

Итак первое что нам нужно сделать, это создать подходящую структуру.

Добавьте следующий код в файл <b>register.liquid</b>:

{% highlight html %}
<h3 class="form_title">Личная информация</h3>
<ul class="form_list">
	<li class="form_item">
		<label for="first_name" class="form_label">Имя:</label>
		<input type="text" placeholder="Имя" id="first_name" class="default_input">
	</li>
	<li class="form_item">
		<label for="last_name" class="form_label">Фамилия:</label>
		<input type="text" placeholder="Фамилия" id="last_name" class="default_input">
	</li>
	<li class="form_item">
		<label for="email" class="form_label">Ваш E-mail:</label>
		<input type="email" placeholder="E-mail" id="email" class="default_input">
	</li>
	<li class="form_item">
		<label for="password" class="form_label">Пароль</label>
		<input type="password" placeholder="Пароль" id="password" class="default_input">
	</li>
	<li class="form_item right_mod">
		<input class="btn" type="submit" value="Отправить">
	</li>
</ul>
{% endhighlight %}

В <b>Shopify</b> есть множество форм под разные нужды. К примеру форма под регистрацию нового пользователя, форма для авторизации, форма активации аккаунта и т.д. В этой статье мы разберем форму которая предназначена для регистрации нового пользователя. Следующее что нам нужно сделать, это добавить к нашей разметке форму, делается это при помощи тега form.

{% highlight html %}
{% raw  %}
{% form 'create_customer' %} 

{% endform % }
{% endraw %}
{% endhighlight %}

Должно получиться следующее:

{% highlight html %}
{% raw %}
<h3 class="form_title">Личная информация</h3>
{% form 'create_customer' %}
	<ul class="form_list"
		<li class="form_item">
			<label for="first_name" class="form_label">Имя:</label>
			<input type="text" placeholder="Имя" id="first_name" class="default_input">
		</li>
		<li class="form_item">
			<label for="last_name" class="form_label">Фамилия:</label>
			<input type="text" placeholder="Фамилия" id="last_name" class="default_input">
		</li>
		<li class="form_item">
			<label for="email" class="form_label">Ваш E-mail:</label>
			<input type="email" placeholder="E-mail" id="email" class="default_input">
		</li>
		<li class="form_item">
			<label for="password" class="form_label">Пароль</label>
			<input type="password" placeholder="Пароль" id="password" class="default_input">
		</li>
		<li class="form_item right_mod">
			<input class="btn" type="submit" value="Отправить">
		</li>
	</ul>
{% endform %}
{% endraw %}
{% endhighlight %}

Здесь мы оборачиваем нашу разметку тегом формы, которая ответственна за создание нового пользователя. Следующее что нам нужно сделать, это добавить атрибут <b>name</b> с нужными значениями в наши инпуты.

Должно получиться следующее:

{% highlight html %}
{% raw %}
<h3 class="form_title">Личная информация</h3>
{% form 'create_customer' %}
	<ul class="form_list"
		<li class="form_item">
			<label for="first_name" class="form_label">Имя:</label>
			<input type="text" placeholder="Имя" name="customer[first_name]" id="first_name" class="default_input">
		</li>
		<li class="form_item">
			<label for="last_name" class="form_label">Фамилия:</label>
			<input type="text" placeholder="Фамилия" name="customer[last_name]" id="last_name" class="default_input">
		</li>
		<li class="form_item">
			<label for="email" class="form_label">Ваш E-mail:</label>
			<input type="email" placeholder="E-mail" name="customer[email]" id="email" class="default_input">
		</li>
		<li class="form_item">
			<label for="password" class="form_label">Пароль</label>
			<input type="password" placeholder="Пароль" name="customer[password]" id="password" class="default_input">
		</li>
		<li class="form_item right_mod">
			<input class="btn" type="submit" value="Отправить">
		</li>
	</ul>
{% endform %}
{% endraw %}
{% endhighlight %}

Здесь мы просто описываем какое поле за что ответственно. Для того чтобы увидеть форму которую мы только что создали, перейдите по url <b>yourshop-name.com/account/register</b> После того как вы заполните все поля и нажмете кнопку <b>Отправить</b> аккаунт должен быть создан. Чтобы проверить так ли это, зайдите в вашей админке в раздел <b>Customers</b> и посмотрите есть ли в списке тот аккаунт который вы только что создали.
<img alt="" src="../../../../i/create-account-4.png">

На этом все, наша форма регистрации полностью готова. Осталось только вам ее хорошо стилизовать и тогда все будет perfect.