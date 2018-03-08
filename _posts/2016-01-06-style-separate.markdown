---
layout: post
title:  "Разбиение стилей с помощью sass"
date:   2016-01-06 08:24:00 +0300
categories: sass
lang: ru
autor: Andrey Boyko
---
Привет, разработчик!

Иногда при верстке бывает необходимо разбить файлы стилей на несколько по какому-нибудь принципу. В этой статье мы разобьем стили на основной файл и файл для IE9.

Структура папок (отображены только те, которые необходимы для данной статьи):

{% highlight html %}
└──assets
    └── sass/
        └── helpers/
                _mixins.scss
        _reset.scss
        _fonts.scss
        _header.scss
        _main.scss
        _includes.scss
        main_global.scss
        ie_9.scss    
└──dist
    └──styles
        main_global.css
        ie_9.css
{% endhighlight %}
Т.к. sass не компилирует файлы начинающиеся на символ "\_" , в итоговом css оказываются только основные файлы. Сейчас это работает по следующей схеме:

main_global.scss - стили всего проекта без IE9<br/>
ie_9.scss - стили всего проекта + IE9

Рассмотрим как это работает.

В файле _mixins.scss объявляем миксин для разделения стилей IE9:
{% highlight scss %}
@mixin ie_9_check {
	@if $ie9 == true {
		@content;
	}
}
{% endhighlight %}

Все отдельные стили включаем в один файл :

_includes.scss
{% highlight scss %}
@import "includes/_mixins";
@import "reset";
@import "fonts";
@import "header";
@import "main";
{% endhighlight %}

Основной файл стилей выглядит так:

{% highlight scss %}
$prefix-for-webkit:    true !default;
$prefix-for-mozilla:   false !default;
$prefix-for-microsoft: true !default;
$prefix-for-opera:     false !default;
$prefix-for-spec:      true !default; // required for keyframe mixin


$develop:true;
$production:false;
$all:true;
$ie9:false;
$ie8:false;
$ie7:false;
$doc:false;
$local_var:global;
$holydat_var:none;

@import "includes";
{% endhighlight %}

Весь файл нам в данный момент не интересен, обратите внимание на два ключевых момента:
{% highlight scss %}
$ie9:false;

@import "includes";
{% endhighlight %}
В наш файл войдут все стили, кроме IE9.
Файл для IE9 будет отличаться только переменной $ie9.

ie_9.scss
{% highlight scss %}
$prefix-for-webkit:    true !default;
$prefix-for-mozilla:   false !default;
$prefix-for-microsoft: true !default;
$prefix-for-opera:     false !default;
$prefix-for-spec:      true !default; // required for keyframe mixin


$develop:true;
$production:false;
$all:true;
$ie9:true;
$ie8:false;
$ie7:false;
$doc:false;
$local_var:global;
$holydat_var:none;

@import "includes";
{% endhighlight %}

Теперь в _main.scss запишем стили и посмотрим итог:

_main.scss
{% highlight scss %}
.container {
	display: flex;

	@include ie_9_check() {
		display:block;
	}
}
@include ie_9_check() {
	.parent {
		display: block;
	}
	.child {
		float:left;
		width: 33%;
	}
}
{% endhighlight %}

main_global.css
{% highlight css %}
.container {
    display: flex;
}
{% endhighlight %}
ie9.css
{% highlight css %}
.container {
    display: flex;
    display: block;
}

.parent {
    display: block;
}

.child {
    float: left;
    width: 33%;
}
{% endhighlight %}

На этом все.
