---
layout: post
title:  "Shopify. Connecting Slick slider"
date:   2018-05-10 10:00:00 +0300
categories: shopify
lang: en
autor:  glivera-team
---

Let’s look at the possibility of creating a slide show using [slick](http://kenwheeler.github.io/slick/) plugin. An important feature will be the ability of the administrator to add, delete slides, and making settings of the slider. For this we will use <b>“sections”</b> and <b>“sections blocks”</b>.

## **1.Connection**

For development of the theme we will use the template engine [slate](https://shopify.github.io/slate/), that’s why the project structure may differ from yours.

Let’s go to the [developer’s site](http://kenwheeler.github.io/slick/) and download the archive with the plugin, connect styles and js.

When I was connecting styles, I used SCSS files, which posted at `src/styles/modules/` and connected in the file `styles/theme.scss`.


{% highlight html %}
{% raw  %}
@import url('modules/slick.scss');
@import url('modules/slick-theme.scss');
{% endraw %}
{% endhighlight %}

Js is located at `src/scripts/vendor/` and connected in file `src/scripts/vendor.js`

{% highlight html %}
{% raw  %}
// =require vendor/slick.min.js
{% endraw %}
{% endhighlight %}

Jquery was connected in the same way. 

We will make the slider initiation in the section file using liquid tag for javascript.

{% highlight html %}
{% raw  %}
{% javascript %}
$('.slider_wrap').slick({
infinite: true,
speed: 500,
slidesToShow: 1,
slidesToScroll: 1
});
{% endjavascript %}
{% endraw %}
{% endhighlight %}


This is the main setting, the rest we will configure via the theme admin panel. 

## **2. The structure**

As I already wrote, the slider will be displayed by the section with the ability of dynamic adding slides and customizing of the feature set. That’s why I go to `src/sections/` and create section file `page-slider.liquid`.
The slider has the most common structure. 

{% highlight html %}
{% raw  %}
{% if section.blocks.size > 0 %}
<div class="slider_wrap" data-slick='{"autoplay": {{ section.settings.carousel_autoplay }}, "autoplaySpeed": {{ section.settings.autoplay_speed }}, "dots": {{ section.settings.carousel_dots }}, "arrows": {{ section.settings.carousel_arrows }}}'>

  {% for block in section.blocks %}
    <div class="slider_item">
        <div class="slider_img_wrap">
          <img src="{{ block.settings.slide_img | img_url: 'original' }}" alt="" class="slider_img">
        </div>
      div class="content_wrap">
      <div class="slider_content">
        <h2 class="slider_title">{{  block.settings.slide_title }}</h2>
        <p class="slider_text">{{  block.settings.slide_text }}</p>
        <a href="{{ block.settings.slide_link }}" class="button">{{ block.settings.button_title }}</a>
      </div>
    </div>
  {% endfor %}
</div>
{% endif %}
{% endraw %}
{% endhighlight %}


`div` with `slider_wrap` class will be the parent element of Slick slider, and exactly there we will display the placeholders in the attribute `data-slick`, which will help us to transmit the configuration values of the slider.

Everything that we have wrapped in liquid tag **`for block in section.blocks`** is the content of our slides. 

The configuration of our section is specified in JSON, which we will put into the **`schema`** tag.

At the top we will have an array of slider configurations, and below we will arrange an array of settings for the block of our slides. At the end we have such JSON.

{% highlight html %}
{% raw  %}
{% schema %}
{
"name": "Carousel",
"max_blocks": 8,
"settings": [
  {
    "type":"header",
    "content":"Carousel option"
  },
  {
    "type": "checkbox",
    "id": "carousel_autoplay",
    "label": "Enable autoplay",
    "default": false
  },
  {
    "type": "checkbox",
    "id": "carousel_dots",
    "label": "Enable dots",
    "default": false
  },
  {
    "type": "checkbox",
    "id": "carousel_arrows",
    "label": "Enable arrows",
    "default": false
  },
  {
    "type": "select",
    "id": "autoplay_speed",
    "label": "Change slides every",
      "options": [
      { "value": "1000", "label": "1 seconds" },
      { "value": "2000", "label": "2 seconds" },
      { "value": "3000", "label": "3 seconds" },
      { "value": "4000", "label": "4 seconds" },
      { "value": "5000", "label": "5 seconds" },
      { "value": "6000", "label": "6 seconds" },
      { "value": "7000", "label": "7 seconds" },
      { "value": "8000", "label": "8 seconds" },
      { "value": "9000", "label": "9 seconds" },
      { "value": "10000", "label": "10 seconds" }
    ],
    "default": "7000"
  }
],
"blocks": [
  {
  "type": "slide",
  "name": "Slide",
  "settings": [
        {
          "type": "text",
          "id": "button_title",
          "label": "Button title"
        },
        {
          "type": "url",
          "id": "slide_link",
          "label": "Slide link"
        },
        {
          "type": "text",
          "id": "slide_title",
          "label": "Slider title"
        },
        {
          "type": "textarea",
          "id": "slide_text",
          "label": "Slide text"
        },
        {
          "type": "image_picker",
          "id": "slide_img",
          "label": "Add img for slide"
        }
      ]
    }
  ]
}
{% endschema %}
{% endraw %}
{% endhighlight %}


At the result we have dynamic slider where we can add/delete the slides, change their order, add the content we need. Also, we can configure the settings like: autoplay, interval of changing slides, switching on/off arrows and dots. 

<img alt="" src="../../../../i/slick1.jpg">