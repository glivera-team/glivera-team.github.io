---
layout: post
title:  "2 Javascript функции для ускорения верстки"
excerpt: "2 Javascript функции для ускорения верстки"
keywords: "верстка, frontend, jquery, тестирование, html, css , javascript"
date:   2016-04-06 08:24:00 +0300
categories: javascript
autor:  Гатилин Максим
---
Привет, разработчик!

Недавно мы в команде задумались, что некоторые вещи мы делаем слишком часто и нам нужно оптимизировать эти процессы. Мы написали 2 функции, которые помогают нам верстать быстрее. Чтобы применить эти функции вам понадобится подключить jQuery(даже если вы отказались от неё, подключите временно).

## Собираем классы со страницы по БЕМу в Sass

В разработке мы применяем следующий подход: у каждого элемента есть один основной класс, остальные классы являются модификаторами. Подробнее об этом можете прочитать в нашей статье <a href="http://glivera-team.github.io/structure/2016/02/11/kbem.html" target="_blank">КБЭМ - Контейнер, Блок, Элемент, Модификатор</a>.
Мы используем sass в качестве препроцессора с синтаксисом scss, и для того чтобы собрать все классы со страницы и вставить их в файлы стилей нам понадобится одна функция:


{% highlight javascript %}
function getAllClasses(context, output) {
	var finalArray = [],
		mainArray = [],
		allElements = $(context).find($('*'));//get all elements of our page
	//If element has class push this class to mainArray
	for (var i = 0; i < allElements.length; i++) {
		var someElement = allElements[i],
			elementClass = someElement.className;
		if (elementClass.length > 0) {//if element have not empty class
			//If element have multiple classes - separate them
			var elementClassArray = elementClass.split(' '),
				classesAmount = elementClassArray.length;
			if (classesAmount === 1) {
				mainArray.push('.' + elementClassArray[0] + ' {');
			} else {
				var cascad = '.' + elementClassArray[0] + ' {';
				for (var j = 1; j < elementClassArray.length; j++) {
					cascad += ' &.' + elementClassArray[j] + ' { }';
				}
				mainArray.push(cascad);
			}
		}
	}

	//creating finalArray, that don't have repeating elements
	var noRepeatingArray = unique(mainArray);
	noRepeatingArray.forEach(function (item) {
		var has = false;
		var preWords = item.split('&');
		for (var i = 0; i < finalArray.length; ++i) {
			var newWords = finalArray[i].split('&');
			if (newWords[0] == preWords[0]) {
				has = true;
				for (var j = 0; j < preWords.length; ++j) {
					if (newWords.indexOf(preWords[j]) < 0) {
						newWords.push(preWords[j]);
					}
				}
				finalArray[i] = newWords.join('&');
			}
		}
		if (!has) {
			finalArray.push(item);
		}
	});
	for (var i = 0; i < finalArray.length; i++) {
		$('<div>' + finalArray[i] + ' }</div>').appendTo(output);
	}


	//function that delete repeating elements from arrays, for more information visit http://mathhelpplanet.com/static.php?p=javascript-algoritmy-obrabotki-massivov
	function unique(A) {
		var n = A.length, k = 0, B = [];
		for (var i = 0; i < n; i++) {
			var j = 0;
			while (j < k && B[j] !== A[i]) j++;
			if (j == k) B[k++] = A[i];
		}
		return B;
	}
}
{% endhighlight %}
В качестве первого параметра мы указываем откуда собрать классы, во втором - куда положить результат.

Напишем небольшую разметку и соберем классы:
{% highlight html %}
<!DOCTYPE html>
<html lang="ru">
  <head>
  <!-- content of the head -->
  </head>
  <body>
    <header class="header"></header>
    <div class="wrapper">
      <div class="base">
        <ul class="article_list">
          <li class="article_item">
            <div class="article_block">
              <h2 class="article_title red_mod">Some tile</h2>
              <div class="article_def">
                <p>some text about article</p>
              </div>
            </div>
          </li>
          <li class="article_item">
            <div class="article_block">
              <h2 class="article_title green_mod">Some tile</h2>
              <div class="article_def">
                <p>some text about article</p>
              </div>
            </div>
          </li>
          <li class="article_item">
            <div class="article_block">
              <h2 class="article_title">Some tile</h2>
              <div class="article_def big_mod">
                <p>some text about article</p>
              </div>
            </div>
          </li>
        </ul>
        <div class="elements_list"></div>
      </div>
    </div>
    <footer class="footer"></footer>
  </body>
</html>
{% endhighlight %}

Вызываем функцию в вашем js-файле:
{% highlight javascript %}
$(document).ready(function ($) {
	getAllClasses('html','.elements_list');
});
{% endhighlight %}
Функция соберет все классы со страницы и положит их в селектор с классом 'elements_list'.Результат:
{% highlight scss %}
.header { }
.wrapper { }
.base { }
.article_list { }
.article_item { }
.article_block { }
.article_title { &.red_mod { }&.green_mod { } }
.article_def { }
.article_title { }
.article_def { &.big_mod { } }
.elements_list { }
.footer { }
{% endhighlight %}

Далее мы копируем классы в редактор в scss, и он расставляет автоформатирование(например, в PHPStorm по комбинации клавиш Ctrl + Alt + L). И в итоге получаем удобный sass файл который можно использовать:
{% highlight scss %}
.header {
}

.wrapper {
}

.base {
}

.article_list {
}

.article_item {
}

.article_block {
}

.article_title {
	&.red_mod {
	}
	&.green_mod {
	}
}

.article_def {
}

.article_title {
}

.article_def {
	&.big_mod {
	}
}

.elements_list {
}

.footer {
}
{% endhighlight %}
Обратите внимание, что модификаторы автоматически подставляются после основных классов.

## Создание быстрой навигации по статичной верстке

Часто мы разрабатываем сайты, в которых страниц больше чем пальцев на руках(а иногда и на ногах). Для того, чтобы прыгать по страницам нужно либо менять адрес в браузере, либо ставить ссылки в разметке(что часто неудобно, запутанно, и бесполезно, т.к. программист поменяет). И поэтому мы написали виджет для быстрой навигации по проекту, который представляет собой одну js-функцию:
{% highlight javascript %}
function pageWidget(pages) {
	var widgetWrap = $('<div class="widget_wrap"><ul class="widget_list"></ul></div>');
	widgetWrap.prependTo("body");
	for (var i = 0; i < pages.length; i++) {
		$('<li class="widget_item"><a class="widget_link" href="' + pages[i] + '.html' + '">' + pages[i] + '</a></li>').appendTo('.widget_list');
	}
	var widgetStilization = $('<style>body {position:relative} .widget_wrap{position:absolute;top:0;left:0;z-index:9999;padding:10px 20px;background:#222;border-bottom-right-radius:10px;-webkit-transition:all .3s ease;transition:all .3s ease;-webkit-transform:translate(-100%,0);-ms-transform:translate(-100%,0);transform:translate(-100%,0)}.widget_wrap:after{content:" ";position:absolute;top:0;left:100%;width:24px;height:24px;background:#222 url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQAgMAAABinRfyAAAABGdBTUEAALGPC/xhBQAAAAxQTFRF////////AAAA////BQBkwgAAAAN0Uk5TxMMAjAd+zwAAACNJREFUCNdjqP///y/DfyBg+LVq1Xoo8W8/CkFYAmwA0Kg/AFcANT5fe7l4AAAAAElFTkSuQmCC) no-repeat 50% 50%;cursor:pointer}.widget_wrap:hover{-webkit-transform:translate(0,0);-ms-transform:translate(0,0);transform:translate(0,0)}.widget_item{padding:0 0 10px}.widget_link{color:#fff;text-decoration:none;font-size:15px;}.widget_link:hover{text-decoration:underline} </style>');
	widgetStilization.prependTo(".widget_wrap");
}
{% endhighlight %}

В качестве аргументов передаем массив со страницами:
{% highlight javascript %}
$(document).ready(function ($) {
	pageWidget(['index','blog','news','about_us','contact']);
});
{% endhighlight %}
Расширение '.html' указываеть не нужно. После вызова этой функции в верхнем левом углу страницы появляется маленький квадрат, наведя на который выдвинется виджет со страницами:
<img alt="" src="../../../../i/widgetcss.gif">

{% highlight html %}
{% endhighlight %}
На этом все.