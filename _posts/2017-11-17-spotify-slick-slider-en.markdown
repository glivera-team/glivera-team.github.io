---
layout: post
title:  "Spotify. Connect Slick slider"
date:   2017-11-17 15:20:00 +0300
categories: spotify
lang: en
autor:  Gavrilov Nikolay
---
# **Spotify. Connect Slick slider**

Consider the possibility of creating a slideshow using the plugin [slick](http://kenwheeler.github.io/slick/). An important feature is that the administrator will be able to add, delete slides, and also be able to make the settings of the slider itself. For this we will use **"sections"** and **"section blocks"**.

## **1.Connection**

To develop the theme, you use the [slate](https://shopify.github.io/slate/) template, so the project structure may be different from yours.

We go to the developer's site and download the archive with the plugin, connect the styles and js.

When connecting styles, I used SCSS files, which I placed on the `src/styles/modules/` path and connected in the file `styles/theme.scss`.

```SCSS
@import url('modules/slick.scss');
@import url('modules/slick-theme.scss');
```

Js is located on the `src/scripts/vendor/` path and is attached in the file `src/scripts/vendor.js`

```javascript
// =require vendor/slick.min.js
```

In the same way, Jquery was connected.

Initiate the slider in the file section using the tag `{%javascript%}`

```liquid
{% javascript %}
  $('.slider_wrap').slick({
    infinite: true,
    speed: 500,
    slidesToShow: 1,
    slidesToScroll: 1
  });
{% endjavascript %}
```

This is the basic setting, the rest will be configured from the admin part of the theme.

## **2. Structure**

As previously, the slider will be displayed in our section, with the ability to dynamically add slides and customize the functionality itself. Therefore I go along the path `src/sections/` and create a file section `page-slider.liquid`.

The structure of the slider is the most common

```HTML
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
```

`div` with the class `slider_wrap` we will be the parent element of the Slick slider and it is there that we display the placeholders in the `data-slick` attribute with which we will pass the configuration values of the slider itself.

All that we wrapped in **`{% for block in section.blocks%}`** is the content content of our slides.

The configuration of our section is specified in JSON which we place in the tag **`{% schema%}`**

At the top, we will have an array of slider configurations, and below we will arrange an array of settings for the block of our slides. At the output, we have a JSON

```liquid
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
```

As a result, we have a dynamic slider, where we can add/remove slides, change their order, add the content we need. Configure the settings of the slider, such as: autoplay, slide change interval, enable/disable arrows and dots.

![slick slider for shopify](https://github.com/glivera-team/Wiki/blob/master/img/slick1.jpg)