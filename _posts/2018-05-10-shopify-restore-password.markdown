---
layout: post
title:  "Shopify: восстановление пароля и сброс"
date:   2018-05-10 10:00:00 +0300
categories: shopify
lang: ru
autor:  glivera-team
---

В этой статье мы рассмотрим создание страницы и шаблона для восстановления пароля, а также будем редактировать файл <b>reset_password.liquid</b> . Работать мы будет с такой формой.
<img alt="shopify: восстановление пароля и сброс" src="../../../../i/restore-password-1.jpg">

## 1.Создание шаблона

Так как шаблона под востановление пароля в Shopify нет, нам придется создать свой шаблон. Это можно сделать через админку. Для этого переходим во вкладку <b>'Themes'</b> выбираем текущую темы, кликаем на кнопку <b>'Actions'</b> и выбираем <b>'Edit code'</b>.
<img alt="shopify: восстановление пароля и сброс" src="../../../../i/restore-password-2.jpg">

После этого создаем новый шаблон при помощи кнопки <b>'Add a new template'</b> выбираем из списка шаблон <b>'page'</b> и даем ему название <b>'restore'</b>, после этого нажимаем на <b>'Create template'</b>.
<img alt="shopify: восстановление пароля и сброс" src="../../../../i/restore-password-3.jpg">

Следующее что нам нужно сделать, это создать страницу восстановления пароля, для этого переходим в админку в пункт <b>'Pages'</b> и создаем новую страницу нажав на кнопку <b>'Add page'</b>.

Вводим название страницы, также в пункте <b>'Template'</b> выбираем тот шаблон который мы только что создали, это <b>'page.restore'</b>. После этого сохраняем нашу страницу.
<img alt="shopify: восстановление пароля и сброс" src="../../../../i/restore-password-4.jpg">

## 2.Создание структуры

Следующее что нам нужно сделать это добавить структуру для нашей формы. Открываем шаблон <b>page.restore</b> и добавляем туда следующий код:

{% highlight html %}
<h2 class="title_h2">Восстановление пароля</h2>
<div class="form_wrap">
	<ul class="input_list">
		<li class="input_item">
			<div class="input_left">
				<label for="user_email" class="input_label">Ваш E-mail:</label>
				<div class="input_descr">Указанный при регистрации</div>
			</div>
			<div class="input_right">
				<input id="user_email" type="email" placeholder="Введите ваш e-mail" name="email" class="form_field default_mod">
			</div>
		</li>
	</ul>
	<div class="btn_wrap">
		<button type="submit" class="btn">Отправить запрос</button>
	</div>
</div>
{% endhighlight %}

После того как структура создана, нам нужно добавить тег <b>'form'</b> с атрибутом <b>'recover_customer_password'</b>. Я думаю по названию атрибута должно быть понятно за что он ответственен) Должно получиться следующее:

{% highlight html %}
{% raw %}
<h2 class="title_h2">Восстановление пароля</h2>
{% form 'recover_customer_password' %}
	<div class="form_wrap">
		<ul class="input_list">
			<li class="input_item">
				<div class="input_left">
					<label for="user_email" class="input_label">Ваш E-mail:</label>
					<div class="input_descr">Указанный при регистрации</div>
				</div>
				<div class="input_right">
					<input id="user_email" type="email" placeholder="Введите ваш e-mail" name="email" class="form_field default_mod">
				</div>
			</li>
		</ul>
		<div class="btn_wrap">
			<button type="submit" class="btn">Отправить запрос</button>
		</div>
	</div>
{% endform %}
{% endraw %}
{% endhighlight %}

Переходим на нашу страницу <b>your-store-name.myshopify.com/pages/restore</b>

После этого вводим в инпут свой <b>email</b> (учетная запись уже должна существовать),  и нажимаем на кнопку <b>'Отправить запрос'</b>. После этого на введенный вами <b>email</b> придет следующее сообщение:
<img alt="shopify: восстановление пароля и сброс" src="../../../../i/restore-password-5.jpg">

Имя магазина понятное дело будет другим. Пока что на кнопку <b>'Reset your password'</b> не нажимаем.

## 3.Создание структуры для шаблона reset_password.liquid

Файл <b>reset_password.liquid</b> он ответственен за сброс пароля. Если у вас его нет, вы также можете его создать в админке выбрав файл <b>'reset_password'</b>
<img alt="shopify: восстановление пароля и сброс" src="../../../../i/restore-password-6.jpg">

Если там что-то есть то удалите все, и добавьте следующий код:

{% highlight html %}
{% raw %}
<h2 class="title_h2">Новый пароль</h2>
{% form 'reset_customer_password' %}
	<div class="form_wrap">
		<ul class="input_list">
			<li class="input_item">
				<div class="input_left">
					<label for="user_email" class="input_label">Ваш новый пароль:</label>
					<div class="input_descr">Придумайте пароль, состоящий как минимум из 8-ми символов</div>
				</div>
				<div class="input_right">
					<input id="user_pass" type="password" placeholder="Придумайте пароль" name="customer[password]" class="form_field default_mod">
					<input type="password" placeholder="Повторите пароль" name="customer[password_confirmation]" class="form_field default_mod">
				</div>
			</li>
		</ul>
		<div class="btn_wrap">
			<button type="submit" class="btn">Вход</button>
		</div>
	</div>
{% endform %}
{% endraw %}
{% endhighlight %}

Здесь мы добавили чуть дополнительной структуры + сразу же добавили форму с атрибутом <b>'reset_customer_password'</b> она ответственна за сброс пароля. Также обязательно чтобы у инпутов были атрибуты name со значениями <b>customer[password]</b> и у второго поля <b>customer[password_confirmation]</b>. После этого сохраняем наш файл. Теперь заходим на почту, и нажимаем на кнопку <b>'Reset your password'</b> и мы попадаем на шаблон <b>reset_password.liquid</b> который мы только что редактировали, здесь вы должны увидеть:

<img alt="" src="../../../../i/restore-password-7.jpg">

Если стилей нет никаких то понятное дело форма у вас будет выглядеть совсем по другому, на добавление стилей в этой статье время мы не будем тратить. После того как вы вписали новый пароль и нажали вход, вы зайдете в свою учетную запись и ваш пароль будет изменен.