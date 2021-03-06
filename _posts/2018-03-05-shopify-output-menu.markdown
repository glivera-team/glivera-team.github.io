---
layout: post
title:  "Shopify: Вывод меню"
date:   2018-03-05 9:25:00 +0300
categories: shopify
lang: ru
autor:  glivera-team
---

В этой статье мы рассмотрим как выводить навигацию в Shopify. Выведем вот такое меню:
<img alt="" src="../../../../i/output-menu-1.jpg">

## 1.Создание меню в админке

Итак первое что нам нужно сделать, это создать меню в нашей админке. Для этого заходим в админку, переходим на вкладку <b>'Navigation'</b> и нажимаем на кнопку <b>'Add menu'</b> как показано на скриншоте:
<img alt="" src="../../../../i/output-menu-2.jpg">

Добавляем наши ссылки кликая по кнопке <b>'Add menu item'</b>, и проставляем нужный url. Также даем имя нашему меню в поле <b>'Title'</b>.

Должно получиться следующее:
<img alt="" src="../../../../i/output-menu-3.jpg">

В каждом созданном меню есть свой <b>'handle'</b>. Благодаря этому мы можем обратиться к конкретному меню которое нам нужно. Этот <b>'handle'</b> можно прописывать также вручную:
<img alt="" src="../../../../i/output-menu-4.jpg">

После того как мы добавили несколько пунктов меню, нажимаем на кнопку <b>'Save menu'</b>, наше меню будет создано. Следующее что нам нужно, это вывести меню в нашу структуру.

## 2.Создание структуры под меню

Давайте создадим подходящую структуру для нашего меню.
Структура у нас будет такая:

{% highlight html %}
<nav class="header_menu">
	<ul class="header_menu_list">
			<li class="header_menu_item">
				<a href="#" class="header_menu_link"></a>
			</li>
	</ul>
</nav>
{% endhighlight %}

Следующее что нам нужно сделать, это циклом вывести все наши ссылки которые лежат у нас в <b>‘Main menu’</b>.

Добавьте следующий код в наш ul:

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

Разберем код чуть подробнее. <b>'linklists'</b> включает в себя все меню которые были созданы вами в админке. <b>'linklist.links'</b> возвращает массив ссылок конкретного меню. Здесь же вы можете увидеть тот <b>'handle'</b> который мы задали в нашей админке - <b>'main-menu'</b>.

<b>'linklists.main-menu.links'</b> тут мы вытаскиваем все ссылки которые есть в <b>'main-menu'</b>. Такой же вывод мы можем делать с другими меню созданными нами в админке, просто нужно подставлять нужный <b>'handle'</b>. <b>{{ link.url }}</b> и <b>{{ link.title }}</b> здесь мы просто подставляем название ссылки и ее url.

Бонусом давайте сделаем еще одну вещь, изменим немного нашу ссылку:

{% highlight html %}
{% raw %}
<a {% if link.active %} class="header_menu_link active_mod" {% endif %} href="{{ link.url }}" class="header_menu_link">{{ link.title }}</a>
{% endraw %}
{% endhighlight %}

Здесь мы делаем проверку, если ссылка на которой мы находимся активная, то добавляем ей соответствующий класс <b>'active_mod'</b>.

На этом все.