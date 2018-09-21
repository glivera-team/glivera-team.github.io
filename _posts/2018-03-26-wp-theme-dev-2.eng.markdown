---
layout: post
title:  "WordPress theme development – Connect your styles and scripts"
date:   2018-03-26
categories: wordpress
lang: eng
autor:  glivera-team
---

## WordPress theme development – Connect your styles and scripts 

### 1. Connect styles

So, we already have the pre-integrated slicing, now we need to set up it on Wordpress, copy the folders **/js** and **/styles** into the theme folder **wp-content/themes/test_themes/**

In **wp-content/themes/test_themes/** we need to create **functions.php**. To connect your styles in **functions.php** to the theme write down:

{% highlight php %}
{% raw %}
function register_styles() {
  wp_register_style( 'theme_styles', get_template_directory_uri() . '/styles/main_global.css' );
  wp_enqueue_style( 'theme_styles' );
}
{% endraw %}
{% endhighlight %}

- **register_styles** – free-handed function name.
- **wp_register_style** – registers CSS file in WordPress. After registration, you can add this file to html document with the help of function **wp_enqueue_style()**.

To connect files like this, not directly in the code, is advantageous for further optimization. If all the files are connected via the WP registration system, thereafter you can use the plugins to combine all the styles into one and give them in a compressed form, on-the-fly. In this way you can connect more than one style files. 

---

###2. Connect scripts

We add the following code in **functions.php**:

{% highlight php %}
{% raw %}
function register_scripts() {
  wp_deregister_script( 'jquery' );
  wp_register_script( 'jquery',  get_template_directory_uri() . '/js/jquery-3.1.1.min.js', array(), '1.0.0', true );
  wp_register_script( 'all',  get_template_directory_uri() . '/js/all.js', array(), '1.0.0', true );
  wp_register_script( 'main',  get_template_directory_uri() . '/js/main.js', array(), '1.0.0', true );

  wp_enqueue_script( 'jquery' );
  wp_enqueue_script( 'all' );
  wp_enqueue_script( 'main' );
}
{% endraw %}
{% endhighlight %}


- **register_scripts** – free-hand function name.

We need registration of the scripts to take under control the embedded scripts: take into account the dependence of some of the others (for example, connect **main.js**  script after we have added the main script **jQuery**) and do not display the same scripts several times. 


---

### Function description

- **wp_deregister_script()** - removes a previously registered script and gives you a possibility to connect yours, in WordPress is already connected some ancient version of jquery. 

- **wp_register_script( $handle, $src, $deps, $ver, $in_footer )** - registers the script file to further connection using a function **wp_enqueue_script()** that saves the order of scripts loading. 

- **$handle (row) (required)** – the name of the script. It must be unique as it will be used to fetch in **wp_enqueue_script()** function.

- **$src (row) (required)** - URL, pathname to the script, for example, http://example.com/wp-content/themes/my-theme/my-theme-script.js. Never write the URL on a direct, if it is in the engine files or plugin, use special pathname functions: **plugins_url()** for plugins and **get_template_directory_uri()** for themes. Deleted scripts should be specified without protocol, like this: //otherdomain.com/js/their-script.js.

- **$deps (array)** -  the array of names for all the registered scripts on which this one depends. The specified scripts will be loaded before the current one. Write false, if you don’t have dependent scripts. 

- **$ver (row)** – The script version that will be added in the pathname end of the file in the form of the argument (?ver=1.1). If you don’t  have the version, write false, in this case WordPress will add the current version of WP to the end. If you write null, no version will be added. This parameter is needed in order to load the correct version of the script by users, bypassing the cache. 

- **$in_footer (logic)** – where to display the script: in the **<head>** or **<footer>**. Usually the scripts are set in the **<head>**. If this parameter will be equal to **true**, the script will be added to the end of the tag **<body>**, for this the theme must have **wp_footer()** function before the closing tag **</body>**.

- **wp_enqueue_script()** – adds the script, only if it hasn’t been added yet and the other scripts which it depends on are registered. Dependent scripts are added automatically. 

---

### Connect example

Add the following code in **functions.php**:

{% highlight php %}
{% raw %}
function theme_setup() {
  add_action( 'wp_enqueue_scripts', 'register_styles' );
  add_action( 'wp_enqueue_scripts', 'register_scripts' );
}

add_action( 'after_setup_theme', 'theme_setup' );
{% endraw %}
{% endhighlight %}

- **theme_setup** – free-hand function name.
- **add_action()** – registers a hook event. During registration, you specify a PHP function that will work at the event time. 
- **after_setup_theme()** – it’s fetched every time the page loads, just after the theme is initialized. Usually we use it to set up the basic capabilities of the theme. 

For example, we have a page with a map from google maps or any other, accordingly we need to connect api maps and styles (in this example we’re connecting the [mapbox map](https://www.mapbox.com/) ). Let’s add the following condition in **functions.php**:

{% highlight php %}
{% raw %}
//register scripts
wp_register_script('mapbox-api', 'https://api.mapbox.com/mapbox.js/v3.1.0/mapbox.js', false, '1.0', false);
wp_register_style( 'mapbox-style', 'https://api.mapbox.com/mapbox.js/v3.1.0/mapbox.css', false, '1.0' );
{% endraw %}
{% endhighlight %}

{% highlight php %}
{% raw %}
//connect on the proper page
<?php if ($post->post_name == 'neighborhood') :
  wp_enqueue_style('mapbox-style');
  wp_enqueue_script('mapbox-api');
endif; ?>
{% endraw %}
{% endhighlight %}

- **$post->post_name** – returns the page labels (**slug**), and if it is equal to the label value of a specific page (in this example it is “neigborhood”, you should show the map only on this page), we will connect the necessary scripts.

We connect the styles in the same way.