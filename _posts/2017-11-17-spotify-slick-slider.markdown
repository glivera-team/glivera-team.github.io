---
layout: post
title:  "Spotify. Подключаем Slick slider"
date:   2017-11-17 13:20:00 +0300
categories: spotify
lang: ru
autor:  Гаврилов Николай
---
# **Spotify. Подключаем Slick slider**

Рассмотрим возможность создания слайдшоу используя плагин [slick](http://kenwheeler.github.io/slick/). Важной особенностью будет то, что администратор будет иметь возможность добавления, удаления слайдов, а также сможет производить настройки функционала самого слайдера. Для этого мы будем использовать **“секции”** и **“блоки секций”**.

## **1.Подключение**

Для разработки темы используется шаблонизатор [slate](https://shopify.github.io/slate/), поэтому структура проекта может отличаться от вашей.

Идем на сайт разработчика и качаем архив с плагином, подключаем стили и js.

При подключении стилей я использовал SCSS файлы, которые разместил по пути `src/styles/modules/` и подключил в файле `styles/theme.scss`.

```SCSS
@import url('modules/slick.scss');
@import url('modules/slick-theme.scss');
```

Js размещен по пути `src/scripts/vendor/` и подключен в файле `src/scripts/vendor.js`

```javascript
// =require vendor/slick.min.js
```

В такой же способ была подключена Jquery.

Инициация слайдера выполним в самом файле секции используя тег `{% javascript %}`

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

Это базовая настройка, остальное будем конфигурировать из админ.части темы.

## **2. Структура**

Как уже ранее писал слайдер у нас будет выводится секцией, с возможностью динамического добавления слайдов и настройкой самого функционала. Поэтому иду по пути `src/sections/` и создаю файл секции `page-slider.liquid`.

Структура слайдера самая обычная

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

`div` с классом `slider_wrap` у нас будет родительским элементом Slick слайдера и именно там мы выводим заполнители в атрибуте `data-slick` с помощью которых мы будем передавать значения конфигурации самого слайдера.

Все что мы обернули в **`{% for block in section.blocks %}`** является контентным  содержимым наших слайдов.

Конфигурацию нашей секции мы зададим в JSON который поместим в тег **`{% schema %}`**

В верхней части у нас будет массив конфигурации слайдера, а ниже мы расположим массив настроек для блока наших слайдов. На выходе имеем такой JSON

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

В итоге у нас получился динамический слайдер, где мы можем добавлять/удалять слайды, менять их порядок, добавлять нужный нам контент. Конфигурировать настройки слайдера, такие как: автоплей, интервал смены слайдов, включение/отключение стрелок и дотов.

![slick slider for shopify](https://github.com/glivera-team/Wiki/blob/master/img/slick1.jpg)