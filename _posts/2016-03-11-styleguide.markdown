---
layout: post
title:  "Создание стайлгайда с помощью sc5"
excerpt: "Создание стайлгайда с помощью sc5"
keywords: "верстка, frontend, дизайн,sc5,стайлгайд,styleguide, html, css , javascript"
date:   2016-03-11 08:24:00 +0300
categories: structure
lang: ru
autor:  Гатилин Максим
---
Привет, разработчик!

При разработке крупных проектов часто требуется создать библиотеку блоков. Это позволяет сделать ваш проект более модульным и контролируемым. Такую библиотеку можно создать с помощью sc5 styleguide. В данном уроке используется sass в качестве препроцессора стилей и gulp для автоматизации. SC5 отлично работает и с другими технологиями, о чем можно прочитать в <a href="https://github.com/SC5/sc5-styleguide" target="_blank">документации</a>. Если вы не знакомы с gulp - у нас на блоге есть <a href="http://glivera-team.github.io/sass/2016/01/07/gulp.html" target="_blank">вводная статья</a>.

## Установка
Устанавливаем sc5 с помощью npm в папке проекта:
{% highlight html %}
npm install  sc5-styleguide
{% endhighlight %}
Для пользователей Windows может потребоваться установка от имени администратора.

Далее в gulpfile.js прописываем зависимость:
{% highlight javascript %}
var styleguide = require('sc5-styleguide');
{% endhighlight %}

## Написание gulp-тасков

Сначала зададим переменные в gulpfile
{% highlight javascript %}
var styleguidePath = 'styleguide'; // папка, куда будет генерироваться стайлгайд
var overviewPath = assetsDir + 'sass/styleguide.md'; //путь до описания стайлгайда
var scssRoot = assetsDir + 'sass/main_global.scss'; // путь до главного sass-файла
{% endhighlight %}

Обратите внимание, что вам нужно создать описание стайлгайда формате markdown и определить переменную для него(которую мы в последующем вставим в таск).

Первый таск - генерация стайлгайда:
{% highlight javascript %}
gulp.task('styleguide:generate', function() {
  return gulp.src(assetsDir + 'sass/**/*.scss')
    .pipe(styleguide.generate({
      title: 'Your styleguide title',
      server: true,
      rootPath: styleguidePath,
      overviewPath: overviewPath,
      extraHead: [
        '<style>html {font-size:10px} body {font-size:16px}</style>'
      ]
    }))
    .pipe(gulp.dest(styleguidePath));
});
{% endhighlight %}
Здесь мы берем все стилевые файлы и на их основе генерируем стайлгайд. Обратите внимание на параметр extraHead - в него передается массив, который потом вставляется в тег \<head> стайлгайда. В данном случае вставлен код с размерами шрифтов, чтобы использовать rem. На данном этапе сгенерируется пустой стайлгайд, так как мы еще ничего не описали в стилях.

Следующий таск - применение стилей внутри стайлгайда:
{% highlight javascript %}
gulp.task('styleguide:applystyles', function() {
  return gulp.src(scssRoot)
    .pipe(sass({
      errLogToConsole: true
    }))
    .pipe(styleguide.applyStyles())
    .pipe(gulp.dest(styleguidePath));
});
{% endhighlight %}
Переменная scssRoot была определена до этого.

Если вы хотите использовать какие-либо файлы проекта внутри стайлгайда - можно их перенести. Для того, чтобы не делать это каждый раз вручную я написал таск:
{% highlight javascript %}
gulp.task('styleguide:copyFonts', function () {
  return gulp.src('')
    .pipe(plumber())
    .pipe(dirSync(assetsDir + 'fonts/', styleguidePath + '/fonts/', {printSummary: true}))
    .pipe(browserSync.stream());
});
{% endhighlight %}
Для синхронизации папок использован плагин <a href="https://www.npmjs.com/package/gulp-directory-sync" target="_blank">gulp-directory-sync</a>.

Далее собираем gulp-таски в один и отправляем его в default и watcher:
{% highlight javascript %}
gulp.task('styleguide', ['styleguide:generate', 'styleguide:applystyles','styleguide:copyFonts']);

gulp.task('default', ['otherTasks',styleguide']);

gulp.task('watch', function () {
  gulp.watch(assetsDir + 'sass/**/*.scss', ['sass','styleguide']);
});
{% endhighlight %}

## Документируем код стилей
Чтобы стайлгайд "ожил" необходимо в стилях прописать комментарии с помощью kss. Синтаксис kss подробно расписан на <a href="http://warpspire.com/kss/syntax/" target="_blank">их сайте</a>.
Выглядит это так:

{% highlight scss %}
// Название компонента
//
// Описание компонента
//
// markup:
//<div class="block">Разметка для компонента</div>
//
// Styleguide 1.1(номер компонента)

.block {
  display:block;
}
{% endhighlight %}

Давайте напишем тестовую страничку для кнопок стайлгайда. Создаем отдельный файл _buttons.scss и внутри него описываем кнопки. Для описания кнопок используется @extend(более подробно система с кнопками изложена в <a href="http://glivera-team.github.io/sass/2015/12/28/sass_btns.html" target="_blank">статье на нашем блоге</a>)

_buttons.scss:
{% highlight scss %}
//common styles for buttons
@mixin button() {
  display: inline-block;
  text-decoration: none;
  text-align: center;
  font-family:sans-serif;

  &:hover, &:focus {
    text-decoration: none;
  }
}

// color types
%button_default {
  color:$white;
  background: $brand_default_color;
  transition:.3s ease;

  &:hover {
    background:darken($brand_default_color,5%);
  }
}

%button_success {
  color:$white;
  background: $brand_success_color;
  transition:.3s ease;

  &:hover {
    background:darken($brand_success_color,5%);
  }
}

%button_danger {
  color:$white;
  background: $brand_danger_color;
  transition:.3s ease;

  &:hover {
    background:darken($brand_danger_color,5%);
  }
}

// size types
%button_big {
  height: 5rem;
  padding:0 3rem;
  line-height: 5rem;
  font-size: 2rem;
  border-radius:8px;

  @include button();
}

%button_standart {
  height: 3.6rem;
  padding:0 2rem;
  font-size: 1.6rem;
  line-height: 3.6rem;
  border-radius: 6px;

  @include button();
}

%button_small {
  height: 2.4rem;
  padding:0 1rem;
  font-size: 1.3rem;
  line-height: 2.4rem;
  border-radius: 4px;

  @include button();
}



// Buttons
//
// All project buttons
//
// Styleguide 1.0

// Default button
//
// .big_mod       - Big button
// .standart_mod - Standart-size button
// .small_mod          - Small button
//
// markup:
//<a href="#" class="btn_default {$modifiers}">Button</a>
//
// Styleguide 1.1

.btn_default {
  @extend %button_default;

  &.big_mod {
    @extend %button_big;
  }

  &.standart_mod {
    @extend %button_standart;
  }

  &.small_mod {
    @extend %button_small;
  }
}

// Success button
//
// .big_mod       - Big button
// .standart_mod - Standart-size button
// .small_mod          - Small button
//
// markup:
//<a href="#" class="btn_success {$modifiers}">Button</a>
//
// Styleguide 1.2

.btn_success {
  @extend %button_success;

  &.big_mod {
    @extend %button_big;
  }

  &.standart_mod {
    @extend %button_standart;
  }

  &.small_mod {
    @extend %button_small;
  }
}

// Danger button
//
// .big_mod       - Big button
// .standart_mod - Standart-size button
// .small_mod          - Small button
//
// markup:
//<a href="#" class="btn_danger {$modifiers}">Button</a>
//
// Styleguide 1.3

.btn_danger {
  @extend %button_danger;

  &.big_mod {
    @extend %button_big;
  }

  &.standart_mod {
    @extend %button_standart;
  }

  &.small_mod {
    @extend %button_small;
  }
}
{% endhighlight %}

Обратите внимание, что мы можем описать модификаторы блока(например .big_mod), а затем поставить параметр {$modifiers}. Стайлгайд сгенерирует для нас все виды кнопок.
<img alt="" src="../../../../i/kss-lesson-1.png">

Если вы хотите создать еще раздел с блоками - просто делайте новый scss-файл(для лучшей наглядности) и опичвайте в нем новые блоки, указывая соответствующие номера для них.

Если вы хотите вставить некоторые блоки вместе - можно не переписывать разметку, а вставить их по номерам через специальный тег \<sg-insert>.

{% highlight javascript %}
// Page header
//
// Main header of the page
//
// markup:
//<header>
//<sg-insert>2.3</sg-insert>
//<sg-insert>2.1</sg-insert>
//</header>
//
// Styleguide 3.0
{% endhighlight %}

## Если стайлгайд не генерируется

Иногда случается, что sc5 не выдавая никакой ошибки просто не генерирует стайлгайд. В этом случае попробуйте обернуть специфичные препроцессорные стили в специальные комментарии:
{% highlight scss %}
// styleguide:ignore:start
@function z-index($key) {
  @return map-get($z-index, $key);
}

@mixin z-index($key) {
  z-index: z-index($key);
}

@mixin font_face($file_name,$font_name:$file_name,$path:$font_path,$weight:normal,$style:normal) {
  @font-face {
    font-family:quote($font_name);
    src: url($path + $file-name + '.eot');
    src: url($path + $file-name + '.eot?#iefix') format('embedded-opentype'),
    url($path + $file-name + '.woff') format('woff'),
    url($path + $file-name + '.ttf') format('truetype'),
    url($path + $file-name + '.svg##{$font_name}') format('svg');
    font-weight: $weight;
    font-style: $style;
  }
}
// styleguide:ignore:end
//
{% endhighlight %}

## Заключение
Разработка через стайлгайд помогает взаимодействию между дизайнером и верстальщиком. Вы получаете страницу с независимыми блоками, а подключив регрессивные тесты - можно быстро отлавливать ошибки верстки. Ваш сайт(или приложение) теперь гораздо легче масштабируется, а наработки из одного проекта легко переносятся в другие.

На этом все.