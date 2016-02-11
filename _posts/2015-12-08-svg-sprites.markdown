---
layout: post
title:  "Как мы используем SVG-спрайты"
date:   2015-12-08 08:24:00 +0300
categories: svg
autor:  Гатилин Максим
---
Привет, разработчик!

При верстке макета из PSD часто иконки вставлены в формате SVG, а если нет — просим их у дизайнера. Ранее мы использовали иконочные шрифты, но недавно увидели преимущества спрайтов и решили попробовать <s>с ними поиграться</s> внедрить их в процесс разработки. Нам нравятся иконочные шрифты, но они имеют ряд недостатков(на эту тему почитайте [CSSTricks](https://css-tricks.com/icon-fonts-vs-svg/)). Эксперимент удался, и вот как мы организовали систему.

## Условия

Что нам нужно от спрайтов:

1. Гибкое управление размером, цветом и поведением(hover, focus etc) иконки
2. Автоматизация, минимум ручной работы
3. Кеширование иконок для хорошей скорости загрузки страниц
4. Удобная вставка иконок в разметку страницы (для шаблонизации html я использую jade)

Структура папок:

{% highlight html %}
├── gulpfile.js                # gulpfile
└──assets                      # здесь редактируем файлы
    └── jade/                  # шаблонизатор html
    └── sass/                  # стили
    └── js/                    # скрипты
    └── i/                     # картинки, сюда мы и будем вставлять спрайт
└──dist                        # здесь получаем готовый проект
{% endhighlight %}

Подробнее о том как работает наша сборка - можете почитать [в репозитории](https://github.com/glivera-team/glivera-team-template).
Для создания спрайта используем gulp, а именно:

* <b>gulp-svg-sprites</b>  - создание спрайта
* <b>gulp-svgmin</b> - минификация SVG 
* <b>gulp-cheerio</b> - удаление лишних атрибутов из svg
* <b>gulp-replace</b> - фиксинг некоторых багов, об этом ниже

## Поехали

Устанавливаем плагины(мы это делаем глобально и потом линкуем):
{% highlight html %}
npm install gulp-svg-sprites gulp-svgmin gulp-cheerio gulp-replace -g
npm link gulp-svg-sprites gulp-svgmin gulp-cheerio gulp-replace
{% endhighlight %}

В gulpfile объявляем плагины:

{% highlight javascript %}
var svgSprite = require('gulp-svg-sprites'),
	svgmin = require('gulp-svgmin'),
	cheerio = require('gulp-cheerio'),
	replace = require('gulp-replace');
{% endhighlight %}

## Варим спрайт
Первый таск - создаем html-файл с тегами <b>symbol</b>.
{% highlight javascript %}
gulp.task('svgSpriteBuild', function () {
	return gulp.src(assetsDir + 'i/icons/*.svg')
		// minify svg
		.pipe(svgmin({
			js2svg: {
				pretty: true
			}
		}))
		// remove all fill and style declarations in out shapes
		.pipe(cheerio({
			run: function ($) {
				$('[fill]').removeAttr('fill');
				$('[style]').removeAttr('style');
			},
			parserOptions: { xmlMode: true }
		}))
		// cheerio plugin create unnecessary string '&gt;', so replace it.
		.pipe(replace('&gt;', '>'))
		// build svg sprite
		.pipe(svgSprite({
				mode: "symbols",
				preview: false,
				selector: "icon-%f",
				svg: {
					symbols: 'symbol_sprite.html'
				}
			}
		))
		.pipe(gulp.dest(assetsDir + 'i/'));
});
{% endhighlight %}
Давайте разберемся, что тут происходит по частям.

Говорим откуда нам нужно взять иконки и минифицируем их. Переменная assetsDir - для удобства.
{% highlight javascript %}
return gulp.src(assetsDir + 'i/icons/*.svg')
	// minify svg
	.pipe(svgmin({
		js2svg: {
			pretty: true
		}
	}))
{% endhighlight %}
Удаляем атрибуты style и fill из иконок, для того чтобы они не перебивали стили, заданные через css.
{% highlight javascript %}
.pipe(cheerio({
	run: function ($) {
		$('[fill]').removeAttr('fill');
		$('[style]').removeAttr('style');
	},
	parserOptions: { xmlMode: true }
}))
{% endhighlight %}
Однако у данного плагина один баг - иногда он преобразовывает символ '>' в кодировку `'&gt;'`.
<br/>Эту проблему решает следующий кусок таска:
{% highlight javascript %}
.pipe(replace('&gt;', '>'))
{% endhighlight %}
Теперь сделаем из получившегося спрайт и положим в папку:
{% highlight javascript %}
.pipe(svgSprite({
		mode: "symbols",
		preview: false,
		selector: "icon-%f",
		svg: {
			symbols: 'symbol_sprite.html'
		}
	}
))
.pipe(gulp.dest(assetsDir + 'i/'));
{% endhighlight %}
<b>symbol_sprite.html</b> - и есть наш спрайт. Внутри он будет содержать следующее(для простоты приведена пара иконок):
{% highlight html %}
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="0" height="0"
     style="position:absolute">

    <symbol id="icon-burger" viewBox="0 0 66 64">
        <path fill-rule="evenodd" clip-rule="evenodd" d="M0 0h66v9H0V0zm0 27h66v9H0v-9zm0 27h66v9H0v-9z"/>
    </symbol>

    <symbol id="icon-check_round" viewBox="-0.501 -0.752 18 18">
        <path d="M8.355 0C3.748 0 0 3.748 0 8.355s3.748 8.355 8.355 8.355 8.355-3.748 8.355-8.355S12.962 0 8.355 0zm0 15.363c-3.865 0-7.01-3.144-7.01-7.01 0-3.864 3.145-7.007 7.01-7.007s7.01 3.144 7.01 7.01-3.146 7.007-7.01 7.007z"/>
        <path d="M11.018 5.69l-3.9 3.9L5.69 8.165c-.262-.263-.688-.263-.95 0-.264.263-.264.69 0 .952l1.9 1.903c.132.13.304.196.476.196s.344-.066.476-.197l4.376-4.378c.263-.263.263-.69 0-.952s-.69-.262-.952 0z"/>
    </symbol>

</svg>
{% endhighlight %}
## Щепотка стилей
Теперь нам нужно сделать стили для нашего спрайта(в данном случае файл .scss). В плагине gulp-svg-sprites мы можем задать этот файл, но вот какая досада - его нельзя сделать при данной настройке:
{% highlight javascript %}
mode: "symbols"
{% endhighlight %}
Сделаем для создания scss отдельный таск.
{% highlight javascript %}
// create sass file for our sprite
gulp.task('svgSpriteSass', function () {
	return gulp.src(assetsDir + 'i/icons/*.svg')
		.pipe(svgSprite({
				preview: false,
				selector: "icon-%f",
				svg: {
					sprite: 'svg_sprite.html'
				},
				cssFile: '../sass/_svg_sprite.scss',
				templates: {
					css: require("fs").readFileSync(assetsDir + 'sass/templates/_sprite-template.scss', "utf-8")
				}
			}
		))
		.pipe(gulp.dest(assetsDir + 'i/'));
});
{% endhighlight %}
В свойстве <b>cssFile </b> объявляем, куда положить на scss файл(потом инклудим его).<br/>
В свойстве <b>templates</b> объявляем, где взять для него шаблон. Код шаблона:
{% highlight css %}
.icon {
	display: inline-block;
	height: 1em;
	width: 1em;
	fill: inherit;
	stroke: inherit;
}
{#svg}
.{name} {
	font-size:{height}px;
	width:({width}/{height})+em;
}
{/svg}
{% endhighlight %}
Получаем _svg_sprite.scss следующего содержания:
{% highlight css %}
.icon {
	display: inline-block;
	height: 1em;
	width: 1em;
	fill: inherit;
	stroke: inherit;
}

.icon-burger {
	font-size:64px;
	width:(66/64)+em;
}

.icon-check_round {
	font-size:18px;
	width:(18/18)+em;
}
{% endhighlight %}
Скомпилированный css будет таким:
{% highlight css %}
.icon {
	display: inline-block;
	height: 1em;
	width: 1em;
	fill: inherit;
	stroke: inherit;
}

.icon-burger {
	font-size: 64px;
	width: 1.03125em;
}

.icon-check_round {
	font-size: 18px;
	width: 1em;
}
{% endhighlight %}
Обратите внимание, что размеры иконок выражены через <b>em</b>, что позволит нам в дальнейшем управлять ими через font-size.<br/>Составляем итоговый таск, и запускаем его:
{% highlight javascript %}
gulp.task('svgSprite', ['svgSpriteBuild', 'svgSpriteSass']);
{% endhighlight %}

## Подключаем на страницу
Итак мы получили html-файл с иконками и scss-файл с оформлением. Далее подключим файл на страницу, используя кеширование через localStorage. Этот процесс подробно описан в статье [Caching SVG Sprite in localStorage](http://osvaldas.info/caching-svg-sprite-in-localstorage).<br/>
Подключаем js-файл следующего содержания:
{% highlight javascript %}
;( function( window, document )
{
	'use strict';

	var file     = 'i/symbol_sprite.html',
		revision = 1;

	if( !document.createElementNS || !document.createElementNS( 'http://www.w3.org/2000/svg', 'svg' ).createSVGRect )
		return true;

	var isLocalStorage = 'localStorage' in window && window[ 'localStorage' ] !== null,
		request,
		data,
		insertIT = function()
		{
			document.body.insertAdjacentHTML( 'afterbegin', data );
		},
		insert = function()
		{
			if( document.body ) insertIT();
			else document.addEventListener( 'DOMContentLoaded', insertIT );
		};

	if( isLocalStorage && localStorage.getItem( 'inlineSVGrev' ) == revision )
	{
		data = localStorage.getItem( 'inlineSVGdata' );
		if( data )
		{
			insert();
			return true;
		}
	}

	try
	{
		request = new XMLHttpRequest();
		request.open( 'GET', file, true );
		request.onload = function()
		{
			if( request.status >= 200 && request.status < 400 )
			{
				data = request.responseText;
				insert();
				if( isLocalStorage )
				{
					localStorage.setItem( 'inlineSVGdata',  data );
					localStorage.setItem( 'inlineSVGrev',   revision );
				}
			}
		}
		request.send();
	}
	catch( e ){}

}( window, document ) );
{% endhighlight %}
Файл подключен, после первой загрузки он кешируется. <b>Если вам нужно нужно обновить спрайт в js-файле приведенном выше меняйте параметр revision на +1.</b>
Иконки вставляем через миксин jade, т.к. это быстро и удобно:
{% highlight javascript %}
mixin icon(name,mod)
	- mod = mod || ''
	svg(class="icon icon-" + name + ' ' + mod)
		use(xlink:href="#icon-" + name)
{% endhighlight %}
Теперь, чтобы встроить иконку вызываем миксин с её именем:
{% highlight javascript %}
+icon('check_round','red_mod')
+icon('burger','green_mod')
{% endhighlight %}
Результирующий html:
{% highlight html %}
<svg class="icon icon-check_round red_mod">
    <use xlink:href="#icon-check_round"></use>
</svg>
<svg class="icon icon-burger green_mod">
    <use xlink:href="#icon-burger"></use>
</svg>
{% endhighlight %}
Открываем страницу в браузере:<br/>

![](https://habrastorage.org/files/86f/e46/37d/86fe4637d9814b3d8199b0007bfede61.png)

Пока размеры иконок в натуральную величину и имеют стандартный цвет. Изменим это(не в сгенерированном файле, а в главном):
{% highlight css %}
.icon-burger {
	font-size:3rem;
	&.green_mod {
		fill:green;
	}

}
.icon-check_round {
	font-size:3rem;
	&.red_mod {
		fill: red;
	}
}
{% endhighlight %}
Результат:<br/>
![](https://habrastorage.org/files/e04/e2b/4a0/e04e2b4a09524a4fac31ce4094629971.png)<br/>


Вот и все, мы получили рабочую систему подключения иконок через спрайты, но есть еще один момент.

## Размытие

К сожалению, не все дизайнеры делают иконки по пиксельной сетке. В этом случае иконки будут "размываться". Если вы экспортируете иконки из иллюстратора вам нужно включить пиксельную сетку и подогнать размер и расположение иконки под пиксельную сетку. Если вы работаете в готовыми svg-файлами - воспользуйтесь сервисом [iconmoon](https://icomoon.io/app/) для их правильного выравнивания.

На этом все.