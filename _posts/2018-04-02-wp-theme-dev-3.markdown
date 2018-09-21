---
layout: post
title:  "Разработка темы wordpress - Header.php"
date:   2018-04-02
categories: wordpress
lang: ru
autor:  glivera-team
---

## Header.php

В папке с темой создаем новый файл **header.php**, из **index.php** вырезаем все от **<!DOCTYPE>** и до **<body>** включительно и вставляем в **header.php**.

{% highlight php %}
{% raw %}
<!DOCTYPE html>
<html <?php language_attributes(); ?>>
<head>
  <meta charset="<?php bloginfo('charset'); ?>"/>
  <meta name="viewport" content="width=device-width"/>
  <meta name="description" content="<?php bloginfo('description')?>"/>
  <title>
    <?php bloginfo('name'); ?>
    <?php is_front_page() ? bloginfo('description') : wp_get_document_title(); ?>
  </title>
  <?php wp_head(); ?>
</head>
<body>
{% endraw %}
{% endhighlight %}

- **language_attributes()** - выводит атрибуты для тега **<html>** со значениями текущего языка и т.д.: **lang="ru-RU"**. В админке: **“Настройки - Общие - Язык сайта”**:

![Настройки - Общие - Язык сайта](../../../../i/wp-dev-1.jpg)

- **bloginfo( $string )** - выводит на экран различную информацию о блоге, которая, в основном, указывается в настройках сайта. Функция **bloginfo()** относится к тегам шаблона и может быть использована в любом месте шаблона.
- **$string (строка)**, название параметра, который нужно получить.

Часто используемые параметры:
- **name** - название блога;
- **description** - короткое описание сайта, которое задается в настройках;
- **template_url** - УРЛ директории текущей темы;
- **rss2_url** - УРЛ RSS 2.0 фида (/feed);
- **comments_rss2_url** - УРЛ RSS 2.0 фида комментариев (/comments/feed);
- **pingback_url** - УРЛ для уведомлений на XML-RPC файл (xmlrpc.php);
- **stylesheet_url** - УРЛ на файл стилей CSS (обычно style.css) текущей темы;
- **charset** - кодировка сайта;
- **version** - используемая версия WordPress;
- **html_type** - Content-Type HTML страницы (обычно text/html).

---

- **is_front_page()** - проверяет отображается ли главная (домашняя) страница сайта. Условный тег.
- **wp_get_document_title()** - получает заголовок текущей страницы (документа), который принято выводить в теге **<title>**.
- **wp_head()** - запускает хук-событие **wp_head**. Вызывается в шапке сайта в файле: **header.php**

**wp_head()** - это тег шаблона, который нужно вставлять перед **</head>**, в файлах темы: **header.php** или **index.php** (если **header.php** не используется).
