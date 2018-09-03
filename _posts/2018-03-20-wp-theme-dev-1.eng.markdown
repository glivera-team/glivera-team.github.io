---
layout: post
title:  "Developing of WordPress theme – Main definitions"
date:   2018-03-20
categories: wordpress
lang: eng
autor:  glivera-team
---
## Main definitions

### Theme
WordPress **theme** – it’s a common design of the site that includes colour, graphics, and text. The theme – this is what is sometimes called ‘skin’ or ‘template’, but it’s neither one nor the other. ‘Skin’ and ‘template’ is a decoration of the site. 

WordPress theme is a place where the code meets design, allowing the site to change basing on some specific conditions specified by the site administrator. 


### Cycle

**Cycle** is a critically important program PHP-code used to output the records. Anyone who wants to develop or customize the WordPress has to deal with the mechanics of the cycle functioning. 

Along with the cycle, WordPress developers have made template tags that are the group of PHP-functions; these functions can be fetched by designers to perform the action or output specific information. The template tags generate the templates files. 


### Template

**Templates** (template files) contain programmed chapters, such as template tags that control the structure and work of the WordPress site. These files take information from your data base MySQL WordPress and generate HTML code, which is read into the web browser. The hierarchy of the templates, essentially the processing order, dictates how the templates will manage almost all the aspects of output, including headers, sidebars, and archives. 

### Archive

**Archives** are dynamically generated list of records; usually they are formed into groups of date, banner, label or author. 

### Plugins
**Plugins** are free-handed functions created to extend the basic core functionality of WordPress. As the dir of WordPress plugins shows, there are plugins that allow to improve almost all possible aspects of WordPress.

### Main template files
Any theme consists of at least two files: **index.php** and **style.css**. These are required files, there are still a lot of unrequired, that expand the theme cababilities and make the development friendlier. 

Create the folder **test_theme** (or any other name for the theme) on the server in the folder **wp-content/themes**, copy there the files **index.php** and **style.css**. You can take pre-integrated file **index.html** and rename it in **index.php** (later we will cut it into separate files). 

In **style.css** you need to add the following: 

{% highlight css %}
{% raw %}
/*

Theme Name: theme-name

Theme URI: home-page-theme

Description: brief-theme-description

Author: your-first name

Author URI: your-URI

Template: write-here-parent-theme-name--unrequired-field

Version: version-number--unrequired-field.

Detailed theme description/License-if-needed.

*/
{% endraw %}
{% endhighlight %}

[Description on Woprdpress Codex.](https://codex.wordpress.org/%D0%A1%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5_%D1%82%D0%B5%D0%BC#.D0.A2.D0.B0.D0.B1.D0.BB.D0.B8.D1.86.D0.B0_.D1.81.D1.82.D0.B8.D0.BB.D0.B5.D0.B9_.D1.82.D0.B5.D0.BC.D1.8B)

Later the theme is available from the WP admin panel.