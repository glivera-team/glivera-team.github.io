---
layout: post
title:  "Иконочные шрифты"
date:   2016-01-06 08:24:00 +0300
categories: svg
autor:  Гатилин Максим
---
Привет, разработчик!

В этой статье я расскажу как создавать и использовать иконочный шрифт с помощью gulp. Используем плагины [gulp-iconfont](https://www.npmjs.com/package/gulp-iconfont) и [gulp-iconfont-css](https://www.npmjs.com/package/gulp-iconfont-css).

## Устанавливаем плагины и прописываем зависимости.
{% highlight html %}
npm install gulp-iconfont@4.0.0 gulp-iconfont-css -g
npm link gulp-iconfont gulp-iconfont-css
{% endhighlight %}
gulpfile.js
{% highlight javascript %}
var iconfont = require('gulp-iconfont'),
    iconfontCss = require('gulp-iconfont-css'),
    fontName = 'somename';
{% endhighlight %}
Параметр fontName обязателен, это имя нашего шрифта.

## Пишем таск
gulpfile.js
{% highlight javascript %}
gulp.task('iconfont', function(){
  gulp.src(['assets/i/icons/*.svg'])
     .pipe(iconfontCss({
        path: 'assets/sass/_icons_template.scss',
        fontName: fontName,
        targetPath: '../../sass/_icons.scss',
        fontPath: '../fonts/icons/'
     }))
     .pipe(iconfont({
        fontName: fontName
     }))
     .pipe(gulp.dest('assets/fonts/icons'));
});
{% endhighlight %}
А теперь подробнее:
{% highlight javascript %}
gulp.src(['assets/i/icons/*.svg'])
{% endhighlight %}
Берем все иконки(путь указывается относительно gulpfile)
Далее внутри таска прописываем шаблон для scss-файла, с помощью которого будут использоваться иконки:
{% highlight javascript %}
path: 'assets/sass/_icons_template.scss'
{% endhighlight %}
Шаблон доступен по ссылке [https://github.com/glivera-team/glivera-team-template/blob/master/assets/sass/_icons_template.scss](https://github.com/glivera-team/glivera-team-template/blob/master/assets/sass/_icons_template.scss)

fontName: fontName - название шрифта, которое объявлено ранее.

fontPath: '../fonts/icons/' - путь для шрифта.

targetPath - путь для scss-файла.

## Создаем шрифт

Предварительно проверив все пути(т.к. у вас может быть другое расположение папок) запускаем таск. Если все прошло правильно, в указанной в параметре fontPath папке лежит шрифт в формате svg, а в targetPath теперь scss файл, который необходимо импортировать в sass вашего проекта. Он состоит из трех основных частей:

Подключение шрифта
{% highlight scss %}
@font-face {
  font-family: "somename";
  src: url('../fonts/icons/somename.eot');
  src: url('../fonts/icons/somename.eot?#iefix') format('eot'),
  url('../fonts/icons/somename.woff') format('woff'),
  url('../fonts/icons/somename.ttf') format('truetype'),
  url('../fonts/icons/somename.svg#somename') format('svg');
}
{% endhighlight %}

Общее объявление тихих классов, присвоение им свойств шрифта
{% highlight scss %}
%icon-button_play2,
%icon-button_plus,
%last_selector {
     &:before {
        font-family: "iconsmoon";
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        font-style: normal;
        font-variant: normal;
        font-weight: normal;
        // speak: none; // only necessary if not using the private unicode range (firstGlyph option)
        text-decoration: none;
        text-transform: none;
     }
}
{% endhighlight %}

Каждому тихому классу - свою иконку !
{% highlight scss %}
%icon-achievement:before {
  content:'\E001';
}
%icon-add:before {
  content:'\E002';
}
%icon-android:before {
  content:'\E003';
}
{% endhighlight %}
## Редактируем шрифт на iconmoon

Для того чтобы шрифт было удобнее использовать, отредактируем его на [iconmoon](https://icomoon.io/app/).
В нашем случае мы загружаем не отдельные иконки, а шрифт целиком(тот самый который сгенерировал gulp).
![](https://habrastorage.org/files/b94/70d/c8b/b9470dc8b636449b8ad12d4999fc5669.png)
После загрузки появится список иконок.
Каждую иконку нужно обработать в визуальном редакторе. Нажимаем на значок редактирования, а потом на иконку.
![](https://habrastorage.org/files/6e9/91c/b7b/6e991cb7bc5f46a3bef95ba81a6a6f13.png)
Откроется окно обработки иконки. Включаем сетку(если она выключена) и выставляем размер 16. В верхем левом углу отображен размер иконки.
![](https://habrastorage.org/files/28c/2d4/1b1/28c2d41b116f4ee1ac82a2d0db4f2ef9.png)
Далее нужно привести иконку к размеру 16x16. Для этого нужно "поиграться" с рамером сетки и размером иконки. Вот мой способ(чисто интуитивно пришел к нему):

текущий размер 17.3, отделяем от него дробную часть - 3.

переводим сетку в режим 20 и увеличиваем иконку на число найденное до этого(3).Далее переводим сетку обратно в 16 - должно получиться целое число, которое доводим до 16.

Итого мы получаем иконку 16X16.
![](https://habrastorage.org/files/e6d/3da/79c/e6d3da79cacb4625a20e1c891ca8acc7.png)
Далее размещаем иконку по центру и оставляем один пустой ряд клеточек по сторонам.
![](https://habrastorage.org/files/002/1d6/28c/0021d628c83145f3af2bc0a101258c13.png)
Иконка готова к использованию. Иногда не получается сделать иконку 16x16 из-за нарушения пиксельной сетки.В таком случае лучше сделать иконку шире.

## Использование
Скачиваем шрифт с iconmoon, для этого выделяем все иконки(select all в меню сета) и нажимаем generate font:
![](https://habrastorage.org/files/2e4/602/a7f/2e4602a7fbfb43019ba6f26b13d2ce96.png)
Далее в настройках прописваем имя шрифта(которое мы придумали ранее)
![](https://habrastorage.org/files/21b/654/a65/21b654a653d54c1cb31daa868b92c7f1.png)
Далее скачиваем архив, копируем файлы шрифта(папка fonts) в проект. Также сохраните файл selection.json, он может понадобиться для редактирования шрифта.
Теперь шрифт можно использовать в проекте.Например мы хотим добавить иконку achievment к какому-либо элементу(назовем его .test_selector)
{% highlight scss %}
.test_selector {
     @extend %icon-achievement;
}
{% endhighlight %}
Иконка добавлена!

Теперь, чтобы изменить её цвет или размер, просто пишем эти свойства в before элемента:
{% highlight scss %}
.test_selector {
  @extend %icon-achievement;
  
  color:red;
  margin:100px;
  font-size: 20px;
  position: relative;
  &:before {
     content:"";
     color: #acb2ff;
     font-size: 50px;
     position: absolute;
     top: -25px;
     left: -50px;
  }
}
{% endhighlight %}
Иконка добавлена:
![](https://habrastorage.org/files/d09/472/293/d094722933a8454fa814c190f86e8bc5.png)
На этом все.

