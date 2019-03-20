---
layout: post
title:  "How we use SVG sprites (new way)"
date:   2019-03-15 10:22:00 +0300
categories: svg
lang: en
autor:  Andrey Boyko
---
Hi, developer!

When making a layout from PSD, icons are often inserted in SVG format, and if not, we ask them from the designer. Earlier we used icon fonts, but recently saw the advantages of sprites and decided <s>to try to play with them</s> to place them into the development process. We like icon fonts, but they have a number of disadvantages (you can read CSSTricks on this topic [CSSTricks](https://css-tricks.com/icon-fonts-vs-svg/)). The experiment was successful, and that's how we have organized the system.

## Conditions

What we need from sprites:

1. Flexible management of the size, colour and behaviour (hover, focus, etc) of  an icon
2. Automation, minimum of manual work
3. Loading only the necessary icons on the page
4. Easy insertion of icons into page layout (I use jade for templating html)

Folder structure:

{% highlight html %}
├── gulpfile.js                 # gulpfile
└──assets                      # here we edit the files
    └── pug/                   # template engine html
    └── sass/                  # styles
    └── js/                    # scripts
    └── i/                     # pictures, here we will put the sprite
└──dist                        # here we get the finished project
{% endhighlight %}

You can read and learn more about how our build works in the [repository](https://github.com/glivera-team/glivera-team-template). To create a sprite, use gulp, namely:

* <b>gulp-svg-sprite</b>  - sprite creation
* <b>gulp-svgmin</b> - SVG minification
* <b>gulp-cheerio</b> - removal of unnecessary attributes from svg
* <b>gulp-replace</b> - fixing some bugs, about it we will tell below

## Let’s go

Install the plugins (we do this globally and then link):
{% highlight html %}
npm install gulp-svg-sprite gulp-svgmin gulp-cheerio gulp-replace -g
npm link gulp-svg-sprite gulp-svgmin gulp-cheerio gulp-replace
{% endhighlight %}

Unveil the plugins in the gulpfile::

{% highlight javascript %}
var svgSprite = require('gulp-svg-sprite'),
	svgmin = require('gulp-svgmin'),
	cheerio = require('gulp-cheerio'),
	replace = require('gulp-replace');
{% endhighlight %}

## Cooking sprite

Create an svg-file with the tags <b>symbol</b>.

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

Remove the `style`, `fill` and `stroke` attributes from the icons so that they do not interrupt the styles specified via css.

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

However, this plugin has one bug - sometimes it converts the symbol '>' to the encoding `'& gt;'`.

This problem is solved by the following piece of task:

{% highlight javascript %}
.pipe(replace('&gt;', '>'))
{% endhighlight %}

Now we will make a sprite from the one we have got and put it in a folder:

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

<b>dest:'../../../sass/_sprite.scss'</b> - we announced where to generate styles for the sprite.

<b>template: assetsDir + "sass/templates/_sprite_template.scss"</b> - mplate code on the basis of which styles for the sprite will be generated.

<b>sprite.svg</b> - is our sprite. Inside it will contain the following (a couple of icons are shown for simplicity):

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

The template code for generating styles is presented on a <a href="https://github.com/glivera-team/glivera-team-template/blob/master/assets/sass/templates/_sprite_template.scss" target="_blank">github</a>.


We get the `_sprite.scss` of the following content:

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

The compiled css will be like this:

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

Please note that the sizes of the icons are expressed in <b>em</b>, which will allow us to further manage them through font-size.

Make the final task, and run it:

{% highlight javascript %}
gulp.task('svgSprite', ['svgSpriteBuild']);
{% endhighlight %}

## Attach to the page

So we have got an svg-file with icons and a scss-file with a design. Insert the icons through the pug mixin as it is fast and convenient:

{% highlight javascript %}
mixin icon(name,mod)
	- mod = mod || ''
	svg(class="icon icon-" + name + ' ' + mod)
		use(xlink:href="i/sprite/sprite.svg#" + name)
{% endhighlight %}

Now, in order to embed an icon we fetch a mixin with its name:

{% highlight javascript %}
+icon('arr_round','red_mod')
{% endhighlight %}

Final html:

{% highlight html %}
 <svg class="icon icon-arr_round red_mod">
    <use xlink:href="i/sprite/sprite.svg#arr_round"></use>
</svg>
{% endhighlight %}

Open the page in the browser:

<img alt="" src="../../../../i/sprite-lesson-2-1.png">

The size of the icon is still full size and they have a standard colour. Let's change this (not in the generated file, but in the main file):

{% highlight scss %}
.icon-arr_round {
	font-size: 10rem;
	&.red_mod {
		color:red;
	}
}
{% endhighlight %}

Result:

<img alt="" src="../../../../i/sprite-lesson-2-2.png">

## Cross-browser compatibility

We connect the icon referring to the external svg-file, unfortunately, not all browsers support such a request. To solve this problem, there is the <a href="https://github.com/jonathantneal/svg4everybody" target="_blank">svg4everybody</a> library. We connect it and initialize it:

{% highlight javascript %}
$(document).ready(function () {
	svg4everybody({});
});
{% endhighlight %}

That's it, we have got a working system for connecting icons via sprites, but there is still one more thing.

## Incorrect Icons

Unfortunately, not all designers make icons on a pixel grid. In this case, the icons will be “blurred”. If you export icons from Illustrator, you need to enable the pixel grid and adjust the size and location of the icon to the pixel grid. If you work in ready-made svg-files, use the [iconmoon](https://icomoon.io/app/) service to properly adjust them. It is also important to convert the stroke for icons. You can read how to do this in the <a href="https://icomoon.io/#docs/stroke-to-fill" target="_blank">iconmoon documentation</a>.
That's all.
