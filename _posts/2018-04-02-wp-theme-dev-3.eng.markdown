---
layout: post
title:  "WordPress theme development - Header.php"
date:   2018-04-02
categories: wordpress
lang: eng
autor:  glivera-team
---

## Header.php

Create a new file **header.php** in the folder with the theme, from **index.php** we cut  everything from **<!DOCTYPE>** to **<body>** inclusive and put into **header.php**. 

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

- **language_attributes()** - displays the attributes for the **<html>** tag with the values of the current language and so on: **lang=”ru-RU”**. In the admin panel: **“Settings - Common – Site language”**:

![Settings - Common – Site language](../../../../i/wp-dev-1.jpg)

- **bloginfo( $string )** - displays different information about the blog on the screen, which is basically indicated in the site settings. The function **bloginfo()** refers to the template tags and can be used anywhere in the template. 
- **$string**, the parameter name that we need to get.

Frequently used parameters:
- **name** - blog name;
- **description** - short site description, which is specified in the settings;
- **template_url** - directory URL of the current theme;
- **rss2_url** - URL RSS 2.0 feed (/feed);
- **comments_rss2_url** - URL RSS 2.0 comments feed (/comments/feed);
- **pingback_url** - URL for notifications to the XML-RPC file (xmlrpc.php);
- **stylesheet_url** - URL to the file of CSS styles (usually style.css) of the current theme;
- **charset** -site encoding;
- **version** - WordPress version in use;
- **html_type** - Content-Type HTML of the page (usually text/html).

---

- **is_front_page()** – checks whether the main (front) page is displayed. Adaptive tag.
- **wp_get_document_title()** – gets the title of the current page (document), which is considered to output in the tag **<title>**.
- wp_head() – starts the hook event wp_head. It is fetched in the header in the file: header.php

**wp_head()** – it is the template tag that we need to write before **</head>**, in the theme files: **header.php** or **index.php** (if **header.php** isn’t used).