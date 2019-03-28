---
layout: post
title:  "Splitting styles with sass"
date:   2016-01-07 08:24:00 +0300
categories: sass
lang: en
autor: Andrey Boyko
---
Hi, developer!

Sometimes during layout, it is necessary to split style files into several ones according to some principle. In this article, we will split the styles into the main file and the file for IE9.

Folder structure (are displayed only those folders that we need for this article):

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

Since sass does not compile files starting with the "\_" character, only the main files are in the final css. Now it works as follows:

main_global.scss - styles of the whole project without IE9<br/>
ie_9.scss - styles of the whole project + IE9

Let’s see how it works.

In the _mixins.scss file, declare a mixin to separate the styles of IE9:
{% highlight scss %}
@mixin ie_9_check {
	@if $ie9 == true {
		@content;
	}
}
{% endhighlight %}

Include all individual styles in one file:

_includes.scss
{% highlight scss %}
@import "includes/_mixins";
@import "reset";
@import "fonts";
@import "header";
@import "main";
{% endhighlight %}

The main style file looks like this:

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

We are not interested in the whole file at the moment, pay attention to two key points:
{% highlight scss %}
$ie9:false;

@import "includes";
{% endhighlight %}
All styles except IE9 will be included in our file.
The file for IE9 will differ only in the $ie9 variable.

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

Now write down the styles in _main.scss we and see the result:

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

That's all.
