---
layout: post
title:  "How we use SVG sprites"
date:   2015-12-09 08:24:00 +0300
categories: svg
lang: en
autor:  Andrey Boyko
---
Hi, developer!

When making a layout from PSD, icons are often inserted in SVG format, and if not, we ask them from  the designer. Earlier we used icon fonts, but recently saw the advantages of sprites and decided to try to play with them to place them into the development process. We like icon fonts, but they have a number of disadvantages (you can read CSSTricks on this topic [CSSTricks](https://css-tricks.com/icon-fonts-vs-svg/)). The experiment was successful, and that's how we have organized the system.

## Conditions

What we need from sprites:

1. Flexible management of the size, colour and behaviour (hover, focus, etc.) of  an icon
2. Automation, minimum of manual work
3. Loading only the necessary icons on the page 
4. Easy insertion of icons into page layout (I use pug for templating html)

Folder structure:

{% highlight html %}
├── gulpfile.js                # gulpfile
└──assets                      # здесь редактируем файлы
    └── pug/                   # шаблонизатор html
    └── sass/                  # стили
    └── js/                    # скрипты
    └── i/                     # картинки, сюда мы и будем вставлять спрайт
└──dist                        # здесь получаем готовый проект
{% endhighlight %}

You can read and learn more about how our build works.  [in the repository](https://github.com/glivera-team/glivera-team-template).
To create a sprite, use gulp, namely:

* <b>gulp-svg-sprites</b>  - sprite creation
* <b>gulp-svgmin</b> - SVG minification
* <b>gulp-cheerio</b> - removal of unnecessary attributes from svg
* <b>gulp-replace</b> - fixing some bugs, about it we will tell below

## Let’s go

Install the plugins (we do this globally and then link):
{% highlight html %}
npm install gulp-svg-sprites gulp-svgmin gulp-cheerio gulp-replace -g
npm link gulp-svg-sprites gulp-svgmin gulp-cheerio gulp-replace
{% endhighlight %}

Unveil the plugins in the gulpfile:

{% highlight javascript %}
var svgSprite = require('gulp-svg-sprites'),
	svgmin = require('gulp-svgmin'),
	cheerio = require('gulp-cheerio'),
	replace = require('gulp-replace');
{% endhighlight %}

## Cooking sprite
The first task is to create an html file with symbol tags.
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

Let's see what is happening here in parts.

We point where we need to get the icons and minify them. The variable assetsDir is for convenience.
{% highlight javascript %}
return gulp.src(assetsDir + 'i/icons/*.svg')
	// minify svg
	.pipe(svgmin({
		js2svg: {
			pretty: true
		}
	}))
{% endhighlight %}

Remove the style, fill attributes from the icons so that they do not interrupt the styles specified via css.
{% highlight javascript %}
.pipe(cheerio({
	run: function ($) {
		$('[fill]').removeAttr('fill');
		$('[style]').removeAttr('style');
	},
	parserOptions: { xmlMode: true }
}))
{% endhighlight %}

However, this plugin has one bug - sometimes it converts the symbol ‘>’ to the encoding '&gt;'.
<br/>This problem is solved by the following piece of task:
{% highlight javascript %}
.pipe(replace('&gt;', '>'))
{% endhighlight %}

Now we will make a sprite from the one we have got and put it in a folder:
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

<b>symbol_sprite.html</b> - is our sprite. Inside it will contain the following (for simplicity, a couple of icons is shown):
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

## Pinch styles
Now we need to make styles for our sprite (in this case the .scss file). We can specify this file in the gulp-svg-sprites plugin, but what a nuisance - it cannot be done with this setting:
{% highlight javascript %}
mode: "symbols"
{% endhighlight %}

Let’s make a separate task for creating scss.
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

In the <b>cssFile </b>property we declare where to put the file on the scss (then we include it).<br/>
In the <b>templates</b>property we declare where to get a template for it. Template code:
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

We get the _svg_sprite.scss as follows:
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

The compiled css will be like this:
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

Please note that the sizes of the icons are expressed in <b>em</b>, which will allow us to manage them through font-size.<br/>
Make the final task, and run it:
{% highlight javascript %}
gulp.task('svgSprite', ['svgSpriteBuild', 'svgSpriteSass']);
{% endhighlight %}

## Connect to the page
So, we have got an html-file with icons and a scss-file with the design. Next, connect the file to the page using caching via localStorage. This process is well described in the article [Caching SVG Sprite in localStorage](http://osvaldas.info/caching-svg-sprite-in-localstorage).<br/>
Connect the js-file as follows:
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

The file is connected and it is cached after the first boot. <b>If you need to update the sprite in the js-file above, change the revision parameter to +1.</b>
We insert the icons through the mixin pug, because it is fast and convenient:
{% highlight javascript %}
mixin icon(name,mod)
	- mod = mod || ''
	svg(class="icon icon-" + name + ' ' + mod)
		use(xlink:href="#icon-" + name)
{% endhighlight %}

Now, in order to embed an icon we fetch a mixin with its name:
{% highlight javascript %}
+icon('check_round','red_mod')
+icon('burger','green_mod')
{% endhighlight %}

Final html:
{% highlight html %}
<svg class="icon icon-check_round red_mod">
    <use xlink:href="#icon-check_round"></use>
</svg>
<svg class="icon icon-burger green_mod">
    <use xlink:href="#icon-burger"></use>
</svg>
{% endhighlight %}

Open the page in the browser:<br/>

![](https://habrastorage.org/files/86f/e46/37d/86fe4637d9814b3d8199b0007bfede61.png)

For now, the sizes of the icons are in full size and have a standard colour. Let's change this (not in the generated file, but in the main file):
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

Result:<br/>
![](https://habrastorage.org/files/e04/e2b/4a0/e04e2b4a09524a4fac31ce4094629971.png)<br/>


That's it, we’ve got a working system for connecting icons via sprites, but there is one more thing.

## Blurring

Unfortunately, not all designers make icons on a pixel grid. In this case, the icons will be “blurred”. If you export icons from the illustrator, you need to initiate the pixel grid and adjust the size and location of the icon to the pixel grid. If you work in ready-made svg-files, use the iconmoon service [iconmoon](https://icomoon.io/app/) to properly align them.

That's all.
