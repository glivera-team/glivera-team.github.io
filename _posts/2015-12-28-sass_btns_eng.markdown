---
layout: post
title:  "Site buttons organization with Sass"
date:   2015-12-28 08:24:00 +0300
categories: sass
autor: Gatilin Maxim
---

Designers often create different sizes and decorations of the site buttons. Some of them can be repeating, some are not. It would be awesome to create system for quickly adding and editing buttons, sass @extend's is the right choice. It's illustration of buttons on typical project:
![](https://hsto.org/files/15b/5bb/ed8/15b5bbed80524f0b80a6d075fc5b4294.png)
HTML for buttons would be constant throughout the entire article:
{% highlight html %}
<ul class="about_btn_list">
    <li class="about_btn_item"><a href="#" class="about_btn blue_mod">Инфраструктура</a></li>
    <li class="about_btn_item"><a href="#" class="about_btn red_mod">Квартиры</a></li>
    <li class="about_btn_item"><a href="#" class="about_btn yellow_mod">Галерея</a></li>
</ul>
{% endhighlight %}

## Bad way
The first idea that may come to mind of developer is to write styles for buttons into classes.
SCSS:
{% highlight scss %}
.about_btn {
	display: block;
	height:50px;
	line-height: 50px;
	text-align: center;
	font-family: 'intro';
	font-size: 12px;
}
{% endhighlight %}
We have created styles for our main class, now let's create color modifiers(variables used for colors):
{% highlight scss %}
.about_btn {
	display: block;
	height: 50px;
	line-height: 50px;
	text-align: center;
	font-family: 'intro';
	font-size: 12px;

	&.blue_mod {
		background: $blue_1;
		color: $white;
		transition: background .3s ease;

		&:hover, &:focus {
			text-decoration: none;
		}

		&:hover {
			background: lighten($blue_1, 5%);
		}
	}
	&.red_mod {
		background: $red_1;
		color: $white;
		transition: background .3s ease;

		&:hover, &:focus {
			text-decoration: none;
		}

		&:hover {
			background: lighten($red_2, 5%);
		}
	}
	&.yellow_mod {
		background: $yellow_1;
		color: $white;
		transition: background .3s ease;

		&:hover, &:focus {
			text-decoration: none;
		}
	}
}
{% endhighlight %}
Now it's quietly easy and clear. But we met new button on another page.
![](https://hsto.org/files/9bb/c0c/a64/9bbc0ca6482d4ce0a83260d01dc90511.png)
This button has the same dimensions as the previous one, but another decoration. Let's break DRY-concept and copy styles from previous buttons:
{% highlight scss %}
.news_more_btn {
	display: block;
	height:50px;
	line-height: 50px;
	text-align: center;
	font-family: 'intro';
	font-size: 12px;
}
{% endhighlight %}
Then we should add decor styles:
{% highlight scss %}
.news_more_btn {
	color:$black_2;
	background: transparent;
	box-shadow:inset 0 0 0 2rem $gray_4;
	transition:all .3s ease;

	&:hover,&:focus {
		text-decoration: none;
	}

	&:hover {
		background: $gray_4;
		color:$white;
	}
}
{% endhighlight %}
Then we should add block styles:
{% highlight scss %}
.news_more_btn {
	width: 186px;
	margin: 20px auto;
}
{% endhighlight %}
Now we have this in the stylesheet:
{% highlight scss %}
.about_btn {
	display: block;
	height: 50px;
	width: 186px;
	margin: 20px auto;
	line-height: 50px;
	text-align: center;
	font-family: 'intro';
	font-size: 12px;

	&.blue_mod {
		background: $blue_1;
		color: $white;
		transition: background .3s ease;

		&:hover, &:focus {
			text-decoration: none;
		}

		&:hover {
			background: lighten($blue_1, 5%);
		}
	}
	&.red_mod {
		background: $red_1;
		color: $white;
		transition: background .3s ease;

		&:hover, &:focus {
			text-decoration: none;
		}

		&:hover {
			background: lighten($red_2, 5%);
		}
	}
	&.yellow_mod {
		background: $yellow_1;
		color: $white;
		transition: background .3s ease;

		&:hover, &:focus {
			text-decoration: none;
		}
	}
}

.news_more_btn {
	display: block;
	height:50px;
	line-height: 50px;
	text-align: center;
	font-family: 'intro';
	font-size: 12px;
	color:$black_2;
	background: transparent;
	box-shadow:inset 0 0 0 2rem $gray_4;
	transition:all .3s ease;

	&:hover,&:focus {
		text-decoration: none;
	}

	&:hover {
		background: $gray_4;
		color:$white;
	}
}
{% endhighlight %}
Then we met new button on another page:
![](https://hsto.org/files/1da/3e5/814/1da3e5814b37466694d683519209233d.png)
It has the same decor, but another dimensions and font-size. We have two ways now:

1. We can continue to copy styles for different buttons and add unique classes
2. We can create special classes for buttons styles and add it to our tags

All of these ways have disadvantages. In first case we copy too much code, and when we need change something we must change it in different places(it's very boring). In another case we add to tags presentation classes, it's bad too(Bootstrap lovers will be against).

But we have third way(right way) - using sass @extends(or it's analogs in another preprocessors).

## The right way
Put your button styles to the new file - buttons.scss, It's very flexible. We will separate it on dimensions(+typography) and color schemes. Let's create classes for dimensions:
{% highlight scss %}
%btn_size_1 {
	display: block;
	height:50px;
	line-height: 50px;
	text-align: center;
	font-family: 'intro';
	font-size: 12px;
}

%btn_size_2 {
	display: block;
	height:70px;
	line-height: 70px;
	text-align: center;
	font-family: 'intro';
	font-size: 16px;
}
{% endhighlight %}
Then we create color schemes:
{% highlight scss %}
%btn_gray_1 {
	color: $black_2;
	background: transparent;
	box-shadow: inset 0 0 0 2rem $gray_4;
	transition: all .3s ease;

	&:hover, &:focus {
		text-decoration: none;
	}

	&:hover {
		background: $gray_4;
		color: $white;
	}
}

%btn_blue_1 {
	background: $blue_1;
	color: $white;
	transition: all .3s ease;

	&:hover, &:focus {
		text-decoration: none;
	}

	&:hover {
		background: lighten($blue_1, 5%);
	}
}

%btn_red_1 {
	background: $red_2;
	color: $white;
	transition: all .3s ease;

	&:hover, &:focus {
		text-decoration: none;
	}

	&:hover {
		background: lighten($red_2, 5%);
	}
}

%btn_yellow_1 {
	background: $yellow_1;
	color: $white;
	transition: all .3s ease;

	&:hover, &:focus {
		text-decoration: none;
	}

	&:hover {
		background: lighten($yellow_1, 5%);
	}
}

%btn_green_1 {
	background: $green_1;
	color: $white;
	transition: all .3s ease;

	&:hover, &:focus {
		text-decoration: none;
	}

	&:hover {
		background: lighten($green_1, 5%);
	}
}
{% endhighlight %}
Now we have flexible system for buttons organization. Our styles would be like this:
{% highlight scss %}
.about_btn {
	@extend %btn_size_1;
	&.blue_mod {
		@extend %btn_blue_1;
	}
	&.red_mod {
		@extend %btn_red_1;
	}
	&.yellow_mod {
		@extend %btn_yellow_1;
	}
}
.news_more_btn {
	@extend %btn_size_1;
	@extend %btn_gray_1;

	width: 186px;
	margin: 20px auto;
}
.show_news_btn {
	@extend %btn_size_2;
	@extend %btn_gray_1;

	display: inline-block;
}
{% endhighlight %}

If we need new color scheme - we just create @extend for it and use. If we change current scheme - edit in one place.

## Key points

* Use @extends instead of @include(less css result size)
* If you have 1-2 buttons on your site, this system would be redundant.
* Use display:block in your extends, you can overwrite it in selector rules

That's all.