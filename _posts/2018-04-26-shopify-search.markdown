---
layout: post
title:  "Shopify: Добавление поиска по сайту"
date:   2018-04-26 10:00:00 +0300
categories: shopify
lang: ru
autor:  glivera-team
---

В этой статье мы будем редактировать шаблон <b>search.liquid</b> в котором пользователь может найти товары магазина, страницы и статьи блога.

В итоге у нас получиться следующее:
<img alt="" src="../../../../i/search-1.jpg">

Перейдите на страницу <b>“yourshop-name.com/search”</b> для того чтобы увидеть страницу с поиском.

## 1.Добавление формы поиска

Первое что нам нужно сделать это добавить форму поиска. Откройте файл <b>search.liquid</b>, и замените его содержимое на следующий код:

{% highlight html %}
<h2 class="title_h2">Поиск</h2>
<div class="search">
	<form action="/search" method="get" class="search_form">
		<div class="search_row">
			<input class="search_input" type="text" name="q" value="Поиск">
			<input type="submit"  class="search_btn">
		</div>
	</form>
</div>
{% endhighlight %}

Теперь вы сможете увидеть на странице такую простую форму:
<img alt="" src="../../../../i/search-2.jpg">

Внешний вид формы будет зависеть от того прописали ли вы какие-то стили для нее или нет, в этой статье на стилях мы не будем сосредотачивать свое внимание.

## 2.Проверка на то был ли поиск выполнен

Прежде чем выводить содержимое результата поиска, будет хорошо сделать проверку на то, действительно ли был отправлен запрос на поиск. Это делается для того, чтобы страница поиска не искала пустую строку при клике. Добавьте код ниже после блока с формой:

{% highlight html %}
{% raw  %}
{% if search.performed %}

{% endif %}
{% endraw %}
{% endhighlight %}

Должно получится следующее:
<img alt="" src="../../../../i/search-3.jpg">

Код <b>{% if search.performed %}</b> возвращает true, если была успешно отправлена форма с атрибутом action = <b>"/ search"</b>.

## 3.Выводим результат поиска через цикл

Сейчас давайте добавим цикл <b>for</b> для того чтобы вывести результаты поиска. Мы хотим просмотреть массив <b>«results»</b>, который является частью объекта search, чтобы увидеть, есть ли что-нибудь для вывода.
Внутри конструкции <b>{% if search.performed %}</b> который мы добавили выше, вставьте код:

{% highlight html %}
{% raw %}
{% if search.performed %}
	{% if search.results_count == 0 %}
		<div class="search_none">Ваш запрос <span class="search_none_result">"{{ search.terms }}"</span> не дал никаких результатов.</div>
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

Должно получиться следующее:
<img alt="" src="../../../../i/search-4.jpg">

Теперь используя форму которую мы сделали раньше найдем текст <b>'lorem'</b>, он у меня встречается в статьях блога.

Результат поиска будет таким:
<img alt="" src="../../../../i/search-5.jpg">

Результат поиска в частности наш текст <b>‘lorem’</b> оборачивается в тег strong с классом <b>highlight</b>, этот класс мы можем стилизовать.

Это подсвечивание делается при помощи фильтра <b>highlight</b>.

Больше про фильтры можете узнать из документации: https://help.shopify.com/themes/liquid/filters/additional-filters
Больше про объект search: https://help.shopify.com/themes/liquid/objects/search

Разберем код чуть поподробнее. <b>{% if search.results_count == 0 %}</b> Если не было ничего найдено, выводится div с текстом о том что по данному запросу ничего нет. <b>{% if item.featured_image %}</b> Этот код проверяет если результат поиска содержит <b>featured_image</b> (то есть изображение продукта). Он проверит является ли результат поиска продуктом.

Если результат поиска продукт, то мы хотим вывести соответствующее изображение к нему и его описание. Вывод будет таким если мы пропишем в поиске название продукта:
<img alt="" src="../../../../i/search-6.jpg">

Если результат поиска не продукт, мы выводим просто текст страницы или статьи блога. Также если поиск не дал никаких результатов будет выводится следующее сообщение:
<img alt="" src="../../../../i/search-7.jpg">

## 4.Добавим пагинацию для результатов поиска

{% highlight html %}
{% raw %}
{% paginate search.results by 5 %}

{% endpaginate %}
{% endraw %}
{% endhighlight %}

Здесь мы говорим что хотим видеть на странице только 5 результатов поиска, остальные будут на других страницах.
<img alt="" src="../../../../i/search-8.jpg">

Что за <b>pagination</b> ?
{% highlight html %}
{% raw %}
{% include 'pagination' %}
{% endraw %}
{% endhighlight %}

Таким образом подключаются сниппеты которые лежат в папке <b>Snippets</b> с расширением liquid, в частности у меня там лежит файл <b>pagination.liquid</b> Код самой пагинации мы разбирать не будем, там все очень просто, но вы можете его просмотреть перейдя по [ссылке] https://gist.github.com/jsnord/69ce7677efc08886e319bfbc2529878c


## 5.Заключение

Эту форму с поиском можно добавить куда угодно, хоть в header, хоть в footer неважно, главное чтобы у нее было в атрибутах <b>'action="/search" method="get"</b>' и у инпута также должен быть атрибут name со значением <b>"q"</b> тогда все будет отрабатывать как надо.