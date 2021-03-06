---
layout: post
title:  "Разработка темы wordpress - Подключаем свои стили и скрипты"
date:   2018-03-26
categories: wordpress
lang: ru
autor:  glivera-team
---
## Подключаем свои стили и скрипты

### 1. Подключаем стили

Итак, у нас уже есть готовая верстка, теперь ее нужно натянуть на Wordpress, копируем папки **/js** и **/styles** в папку темы **wp-content/themes/test_themes/**

В **wp-content/themes/test_themes/** создать **functions.php**. Для того чтобы подключить к теме свои стили в **functions.php** пишем:

{% highlight php %}
{% raw %}
function register_styles() {
  wp_register_style( 'theme_styles', get_template_directory_uri() . '/styles/main_global.css' );
  wp_enqueue_style( 'theme_styles' );
}
{% endraw %}
{% endhighlight %}

- **register_styles** - произвольное имя функции.
- **wp_register_style** - регистрирует CSS файл в WordPress. После регистрации файл можно добавить в html документ с помощью функции **wp_enqueue_style()**.

Подключать файлы так, а не напрямую в коде, выгодно для дальнейшей оптимизации. Если все файлы подключены через систему регистрации WP, то затем можно использовать плагины, чтобы объединить все стили в один и отдавать их в сжатом виде, на лету. Так можно подключить не один, а несколько файлов стилей.

---

### 2. Подключаем скрипты

В **functions.php** добавляем следующий код:

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

- **register_scripts** - произвольное имя функции.

Регистрация скриптов нужна, чтобы взять под контроль внедряемые скрипты: учесть зависимость одних от других (например, подключать **main.js** скрипт после того как будет добавлен основной скрипт **jQuery**) и не выводить одни и те же скрипты по несколько раз.

---

### Описание функций

- **wp_deregister_script()** - удаляет ранее зарегистрированный скрипт и дает возможность подключить свой, по умолчанию в Wordpress уже подключен jquery какой-то древней версии.

- **wp_register_script( $handle, $src, $deps, $ver, $in_footer )** - регистрирует файл скрипта, чтобы в дальнейшем подключать его используя функцию **wp_enqueue_script()**, которая сохраняет последовательность загрузки скриптов.

  - **$handle** (строка) (обязательный) - название скрипта. Должно быть уникальным, так как оно будет использоваться для вызова в дальнейшем в функции **wp_enqueue_script()**.
  - **$src** (строка) (обязательный) - URL, путь до скрипта, например, http://example.com/wp-content/themes/my-theme/my-theme-script.js. Никогда не пишите URL напрямую, если он лежит в файлах движка или плагине, используйте специальные функции путей: **plugins_url()** для плагинов и **get_template_directory_uri()** для тем. Удаленные скрипты указывайте без протокола, так: **//otherdomain.com/js/their-script.js**.
  - **$deps** (массив) - массив названий всех зарегистрированных скриптов, от которых зависит этот. Указанные тут скрипты будут загружены перед текущим. Укажите false, если нет зависимых скриптов.
  - **$ver** (строка) - Версия скрипта, которая будет добавлена в конец пути к файлу в виде аргумента (?ver=1.1). Если версии нет укажите false, тогда WordPress добавит в конец текущую версию WP. Если указать null, никакая версия не будет добавлена. Этот параметр нужен, чтобы корректная версия скрипта была загружена пользователями, в обход кэша.
  - **$in_footer** (логический) - где выводить скрипт: в **<head>** или **<footer>**. Обычно скрипты располагаются в **<head>** части. Если этот параметр будет равен **true** скрипт будет добавлен в конец тега **<body>**, для этого тема должна содержать функцию **wp_footer()** перед закрывающим тегом **</body>**.

- **wp_enqueue_script()** - добавляет скрипт, только если он еще не был добавлен и другие скрипты от которых он зависит зарегистрированы. Зависимые скрипты добавляются автоматически.

---

### Пример подключения

В **functions.php** добавляем код:

{% highlight php %}
{% raw %}
function theme_setup() {
  add_action( 'wp_enqueue_scripts', 'register_styles' );
  add_action( 'wp_enqueue_scripts', 'register_scripts' );
}

add_action( 'after_setup_theme', 'theme_setup' );
{% endraw %}
{% endhighlight %}

- **theme_setup** - произвольное имя функции.
- **add_action()** - регистрирует хук-событие. При регистрации указывается PHP функция, которая сработает в момент события.
- **after_setup_theme()** - вызывается каждый раз при загрузке страницы, сразу после того, как тема инициализирована. Обычно используется для того, чтобы установить базовые возможности темы.

Например, есть страница с картой google maps или любой другой, соответственно нам нужно подключить api карты и стили (в данном примере подключаем карту [mapbox](https://www.mapbox.com/) ). В **functions.php** добавляем условие:

{% highlight php %}
{% raw %}
//регистрируем скрипты
wp_register_script('mapbox-api', 'https://api.mapbox.com/mapbox.js/v3.1.0/mapbox.js', false, '1.0', false);
wp_register_style( 'mapbox-style', 'https://api.mapbox.com/mapbox.js/v3.1.0/mapbox.css', false, '1.0' );
{% endraw %}
{% endhighlight %}


{% highlight php %}
{% raw %}
//подключаем на нужной странице
<?php if ($post->post_name == 'neighborhood') :
  wp_enqueue_style('mapbox-style');
  wp_enqueue_script('mapbox-api');
endif; ?>
{% endraw %}
{% endhighlight %}

- **$post->post_name** - возвращает ярлык страницы (**slug**), и если он равен значению ярлыка определенной страницы (в данном примере это “neigborhood”, только на этой странице надо показать карту), то подключим необходимые нам скрипты.

Таким же образом подключаются стили.