---
layout: post
title:  "2 Javascript functions to speed up the layout"
excerpt: "2 Javascript functions to speed up the layout"
keywords: "frontend, jquery, testing, html, css , javascript"
date:   2019-03-17 15:04:00 +0300
categories: javascript
lang: en
autor:  Andrey Boyko
---
Hi, developer!

Recently, our team thought that some things we do too often, and we need to optimize these processes. We have written 2 functions that help us to slice faster. To use these functions you need to connect jQuery (even if you have declaimed from it, connect it temporarily).

## Collect classes from the BEM page in Sass

During development, we use the following approach: each element has one main class, the other classes are modifiers. You can read more about it in our CBEM article - <a href="http://glivera-team.github.io/structure/2016/02/11/kbem-en.html">Container, Block, Element, Modifier</a>. We use sass as a preprocessor with the scss syntax, and in order to collect all the classes from the page and insert them into the style files we will need one function:

In the first parameter, we indicate where to collect the classes, in the second - where to put the result.

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

Let's write a small markup and collect the classes:

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

Fetch the function in your js-file:

{% highlight javascript %}
$(document).ready(function ($) {
	getAllClasses('html','.elements_list');
});
{% endhighlight %}

The function will collect all the classes from the page and put them into the selector with the class <mark>`elements_list`</mark>. The result is:

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

Next what we do, we copy the classes to the editor in scss, and it arranges auto-formatting (for example, in PHPStorm using the key combination Ctrl+Alt+L). And in the end, we get a convenient sass file that can be used:

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

Note that modifiers are automatically inserted after the main classes.

## Creating quick navigation on a static layout

Quite often we develop websites which have more pages than a person has fingers on his hand (and sometimes even on legs). In order to jump through the pages you need to either change the address in the browser, or put links in the markup (which is often inconvenient, confusing, and useless, because the programmer changes this). So, we wrote a widget for quick navigation on the project, which is a single js-function:

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

As arguments, we pass an array with pages:

{% highlight javascript %}
$(document).ready(function ($) {
	pageWidget(['index','blog','news','about_us','contact']);
});
{% endhighlight %}

You don’t need to specify the extension <mark>.html</mark>. After fetching this function, a small square appears in the upper left corner of the page, by hovering on which the widget with the pages will come out:

<img alt="" src="../../../../i/widgetcss.gif">

That's all.