---
layout: post
title:  "Как мы используем SVG-спрайты(новый способ)"
date:   2016-06-13 08:24:00 +0300
categories: svg
lang: ru
autor:  Гатилин Максим
---
Привет, разработчик!

При верстке макета из PSD часто иконки вставлены в формате SVG, а если нет — просим их у дизайнера. Ранее мы использовали иконочные шрифты, но недавно увидели преимущества спрайтов и решили попробовать <s>с ними поиграться</s> внедрить их в процесс разработки. Нам нравятся иконочные шрифты, но они имеют ряд недостатков(на эту тему почитайте [CSSTricks](https://css-tricks.com/icon-fonts-vs-svg/)). Эксперимент удался, и вот как мы организовали систему.

## Условия

Что нам нужно от спрайтов:

1. Гибкое управление размером, цветом и поведением(hover, focus etc) иконки
2. Автоматизация, минимум ручной работы
3. Подгрузка на страницу только необходимых иконок
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

* <b>gulp-svg-sprite</b>  - создание спрайта
* <b>gulp-svgmin</b> - минификация SVG 
* <b>gulp-cheerio</b> - удаление лишних атрибутов из svg
* <b>gulp-replace</b> - фиксинг некоторых багов, об этом ниже

## Поехали

Устанавливаем плагины(мы это делаем глобально и потом линкуем):
{% highlight html %}
npm install gulp-svg-sprite gulp-svgmin gulp-cheerio gulp-replace -g
npm link gulp-svg-sprite gulp-svgmin gulp-cheerio gulp-replace
{% endhighlight %}

В gulpfile объявляем плагины:

{% highlight javascript %}
var svgSprite = require('gulp-svg-sprite'),
	svgmin = require('gulp-svgmin'),
	cheerio = require('gulp-cheerio'),
	replace = require('gulp-replace');
{% endhighlight %}

## Готовим спрайт
Создаем svg-файл с тегами <b>symbol</b>.
{% highlight javascript %}
gulp.task('svgSpriteBuild', function () {
	return gulp.src(assetsDir + 'i/icons/*.svg')
	// minify svg
		.pipe(svgmin({
			js2svg: {
				pretty: true
			}
		}))
		// remove all fill, style and stroke declarations in out shapes
		.pipe(cheerio({
			run: function ($) {
				$('[fill]').removeAttr('fill');
				$('[stroke]').removeAttr('stroke');
				$('[style]').removeAttr('style');
			},
			parserOptions: {xmlMode: true}
		}))
		// cheerio plugin create unnecessary string '&gt;', so replace it.
		.pipe(replace('&gt;', '>'))
		// build svg sprite
		.pipe(svgSprite({
			mode: {
				symbol: {
					sprite: "../sprite.svg",
					render: {
						scss: {
							dest:'../../../sass/_sprite.scss',
							template: assetsDir + "sass/templates/_sprite_template.scss"
						}
					}
				}
			}
		}))
		.pipe(gulp.dest(assetsDir + 'i/sprite/'));
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
Удаляем атрибуты style, fill и stroke из иконок, для того чтобы они не перебивали стили, заданные через css.
{% highlight javascript %}
.pipe(cheerio({
	run: function ($) {
		$('[fill]').removeAttr('fill');
		$('[stroke]').removeAttr('stroke');
		$('[style]').removeAttr('style');
	},
	parserOptions: {xmlMode: true}
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
	mode: {
		symbol: {
			sprite: "../sprite.svg",
			render: {
				scss: {
					dest:'../../../sass/_sprite.scss',
					template: assetsDir + "sass/templates/_sprite_template.scss"
				}
			}
		}
	}
}))
.pipe(gulp.dest(assetsDir + 'i/sprite/'));
{% endhighlight %}
<b>dest:'../../../sass/_sprite.scss'</b> - здесь мы объявили, куда нужно генерировать стили для спрайта.

<b>template: assetsDir + "sass/templates/_sprite_template.scss"</b> - код шаблона, на основе которого будут генерироваться стили для спрайта.

<b>sprite.svg</b> - это наш спрайт. Внутри он будет содержать следующее(для простоты приведена пара иконок):
{% highlight html %}
<?xml version="1.0" encoding="utf-8"?>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<symbol viewBox="0 0 32 32" id="check">
    <path d="M26.664 6.27a.829.829 0 0 0-1.177 0L13.088 18.611a.826.826 0 0 1-1.178 0l-5.433-5.532a.825.825 0 0 0-1.177 0l-2.401 2.158a.83.83 0 0 0-.246.583c0 .215.087.44.247.603l5.478 5.749c.324.328.855.861 1.178 1.186l2.355 2.374a.834.834 0 0 0 1.178 0L29.019 9.83a.846.846 0 0 0 0-1.188l-2.356-2.373z"/>
</symbol>
<symbol viewBox="0 0 94 13" id="rating">
    <path d="M7 10.5l-4.11 2.16.78-4.58L.34 4.84l4.6-.67L7 0l2.06 4.17 4.6.67-3.33 3.24.78 4.58L7 10.5zm20 0l-4.11 2.16.78-4.58-3.33-3.24 4.6-.67L27 0l2.06 4.17 4.6.67-3.33 3.24.78 4.58L27 10.5zm20 0l-4.11 2.16.78-4.58-3.33-3.24 4.6-.67L47 0l2.061 4.17 4.6.67-3.33 3.24.779 4.58L47 10.5zm20 0l-4.109 2.16.779-4.58-3.33-3.24 4.6-.67L67 0l2.061 4.17 4.6.67-3.33 3.24.779 4.58L67 10.5zm24.771 3.073L87 11.064l-4.771 2.509.904-5.318-3.868-3.764 5.343-.778L87-1.128l2.393 4.841 5.343.778-3.868 3.764.903 5.318zM87 9.936l3.447 1.812-.654-3.842 2.792-2.717-3.856-.562L87 1.128l-1.729 3.499-3.856.562 2.792 2.717-.654 3.842L87 9.936z"/>
    <path d="M87 10.5l-4.109 2.16.779-4.58-3.33-3.24 4.6-.67L87 0v10.5z"/>
</symbol>
</svg>
{% endhighlight %}
Код шаблона для генерирования стилей представлен на <a href="https://github.com/glivera-team/glivera-team-template/blob/master/assets/sass/templates/_sprite_template.scss" target="_blank">гитхабе</a>.


Получаем _sprite.scss следующего содержания:
{% highlight css %}
.icon {
	display: inline-block;
	width: 1em;
	height: 1em;
	fill: currentColor;
}

.icon-arr_round {
	font-size:(59/10)*1rem;
	width:(63/59)*1em;
}
.icon-check {
	font-size:(32/10)*1rem;
	width:(32/32)*1em;
}
.icon-rating {
	font-size:(13/10)*1rem;
	width:(94/13)*1em;
}
.icon-save {
	font-size:(57/10)*1rem;
	width:(51/57)*1em;
}
{% endhighlight %}
Скомпилированный css будет таким:
{% highlight css %}
.icon {
  display: inline-block;
  width: 1em;
  height: 1em;
  fill: currentColor;
}

.icon-arr_round {
  font-size: 5.9rem;
  width: 1.0678em;
}

.icon-check {
  font-size: 3.2rem;
  width: 1em;
}

.icon-rating {
  font-size: 1.3rem;
  width: 7.23077em;
}

.icon-save {
  font-size: 5.7rem;
  width: 0.89474em;
}
{% endhighlight %}
Обратите внимание, что размеры иконок выражены через <b>em</b>, что позволит нам в дальнейшем управлять ими через font-size.<br/>Составляем итоговый таск, и запускаем его:
{% highlight javascript %}
gulp.task('svgSprite', ['svgSpriteBuild', 'svgSpriteSass']);
{% endhighlight %}

## Подключаем на страницу
Итак мы получили svg-файл с иконками и scss-файл с оформлением.
Иконки вставляем через миксин jade, т.к. это быстро и удобно:
{% highlight javascript %}
mixin icon(name,mod)
	- mod = mod || ''
	svg(class="icon icon-" + name + ' ' + mod)
		use(xlink:href="i/sprite/sprite.svg#" + name)
{% endhighlight %}
Теперь, чтобы встроить иконку вызываем миксин с её именем:
{% highlight javascript %}
+icon('arr_round','red_mod')
{% endhighlight %}
Результирующий html:
{% highlight html %}
 <svg class="icon icon-arr_round red_mod">
    <use xlink:href="i/sprite/sprite.svg#arr_round"></use>
</svg>
{% endhighlight %}
Открываем страницу в браузере:<br/>

<img alt="" src="../../../../i/sprite-lesson-2-1.png">

Пока размеры иконки в натуральную величину и имеют стандартный цвет. Изменим это(не в сгенерированном файле, а в главном):
{% highlight scss %}
.icon-arr_round {
	font-size: 10rem;
	&.red_mod {
		color:red;
	}
}
{% endhighlight %}
Результат:<br/>
<img alt="" src="../../../../i/sprite-lesson-2-2.png">

## Кроссбраузерность
Мы подключаем иконку ссылаясь на внешний svg-файл, к сожалению не все браузеры поддерживают такой запрос. Для решения этой проблемы есть библиотека <a href="https://github.com/jonathantneal/svg4everybody" target="_blank">svg4everybody</a>. Подключаем её и инициализируем:
{% highlight javascript %}
$(document).ready(function () {
	svg4everybody({});
});
{% endhighlight %}

Вот и все, мы получили рабочую систему подключения иконок через спрайты, но есть еще один момент.

## Некорректные иконки

К сожалению, не все дизайнеры делают иконки по пиксельной сетке. В этом случае иконки будут "размываться". Если вы экспортируете иконки из иллюстратора вам нужно включить пиксельную сетку и подогнать размер и расположение иконки под пиксельную сетку. Если вы работаете в готовыми svg-файлами - воспользуйтесь сервисом [iconmoon](https://icomoon.io/app/) для их правильного выравнивания. Также важно конвертировать stroke для иконок, о том как это сделать написано <a href="https://icomoon.io/#docs/stroke-to-fill" target="_blank">в документации iconmoon</a>.

На этом все.